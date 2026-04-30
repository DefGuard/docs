---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/production-deployment-verification-guide
---

# Production deployment verification guide

This guide helps you verify that your Defguard instance is operational, reachable through the expected network paths, and properly secured.

The process will consist of the following steps:

1. Verifying the configuration of your firewall rules.
2. Verifying your DNS resolution.
3. Testing the whole configuration.

## Prerequisites

Before proceeding, ensure that you deployed your Defguard environment according to the [recommendations](hardware-os-network-and-firewall-recommendations.md) and that the following components are operational:

* 1 server running Defguard Core - the control plane and web interface
  * Located in an internal network segment (not exposed to the Internet)
  * Reachable internally under a domain such as defguard.example.com
* 1 server running Defguard Edge - the public enrollment and client-configuration service
  * Located in a DMZ network segment
  * Publicly accessible from the Internet under a domain such as edge.example.com
* 1 server running Defguard Gateway - the WireGuard VPN endpoint
  * Located in a DMZ network segment
  * Publicly accessible from the Internet under a domain such as vpn.example.com
* A workstation with the Defguard Desktop Client installed and configured to test VPN connectivity.

### Tools required for testing

The tests in this guide use `nmap` and `dig`. Make sure both are installed on your workstation before proceeding. If `nmap` is not available, `nc -zv` can substitute for basic TCP port checks. If `dig` is not available, `host` or `nslookup` can be used instead. `TCP SYN` scans (`nmap -sS`) require elevated privileges - run with `sudo` or as `root`.

{% hint style="warning" %}
Replace all example domains (defguard.example.com, edge.example.com, vpn.example.com) with your actual hostnames throughout every command in this guide.
{% endhint %}

## Verify firewall policies

Confirm that your firewall rules align with Defguard’s secure deployment model.

| Component | Allowed inbound                                                                                           | Blocked inbound           | Notes                                                                                                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------------- | ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Core      | <ul><li>TCP 443 (from internal/VPN only)</li></ul>                                                        | All public traffic        | Core should never be directly exposed to the Internet. Core initiates outbound gRPC connections to Edge and Gateway - it does not receive inbound gRPC connections from them. |
| Edge      | <ul><li>TCP 443 (from public Internet)</li><li>gRPC server port (from Core, internal only)</li></ul>      | All other inbound traffic | Used for enrollment and client configuration. gRPC port must not be reachable from the public internet.                                                                       |
| Gateway   | <ul><li>UDP VPN port (from public Internet)</li><li>gRPC server port (from Core, internal only)</li></ul> | All other inbound traffic | Only VPN traffic and Core communication should be allowed. gRPC port must not be reachable from the public internet.                                                          |

## Verify DNS resolution

Proper DNS configuration ensures that each Defguard component resolves to the correct IP address and network zone.

Run:

```
dig +short vpn.example.com
dig +short edge.example.com
dig +short defguard.example.com
```

Expected results:

| Domain               | Expected IP Type    | Description                                            |
| -------------------- | ------------------- | ------------------------------------------------------ |
| vpn.example.com      | Public IP           | Gateway server reachable from the Internet             |
| edge.example.com     | Public IP           | Edge server for enrollment and configuration           |
| defguard.example.com | Private/Internal IP | Core server, accessible only from internal/VPN network |

## Verify Core outbound connectivity

Core must be able to reach Edge and Gateway on their gRPC management ports. Run the following commands **on the Core server itself** (not from your workstation):

```
 nc -zv <edge-internal-address> 50051
 nc -zv <gateway-internal-address> 50066
```

Replace the placeholders with the internal addresses of your Edge and Gateway servers and update the gRPC ports if using non-default ones.

Expected output:

```
Connection to <edge-internal-address> 50051 port [tcp/*] succeeded!
Connection to <gateway-internal-address> 50066 port [tcp/*] succeeded!
```

Interpretation:

* A successful connection confirms Core's egress firewall allows outbound access to both management ports.
* If either connection fails, check the egress rules on the Core server and the ingress rules on the target server.

## Test the environment

After you've confirmed the proper network segmentation it's time to test it.

### Testing while disconnected from the VPN

This phase verifies firewall segmentation: Edge and Gateway must be reachable to serve unenrolled clients, while Core must be completely hidden from the Internet.

Perform the following tests from the workstation where the Defguard Desktop Client is installed.

Make sure the client is disconnected before running any commands.

In this state:

* ❌ You should not be able to reach the Defguard Core server.
* ✅ You should be able to reach the Defguard Edge server.
* ✅ You should be able to reach the Defguard Gateway server (UDP port for VPN).

#### Test: Defguard Core server reachability and ports

Check the open ports on your Defguard Core server (replace the example domain with your actual one):

```
sudo nmap -Pn -sS defguard.example.com
```

Expected output depends on your specific DNS setup:

```bash
# Option 1 - split-horizon DNS (domain has no public record):                                                                                                                                                                                                                                                                                                                                                       
Failed to resolve "defguard.example.com".

# Option 2 - domain resolves to a private IP from public DNS:
Host seems down.

```

Interpretation:

* The Core server is not reachable when disconnected from the VPN, which is the expected and secure configuration.
* If you see open ports in the output, Core is publicly accessible and your firewall rules need to be reviewed.

#### Test: Defguard Edge Server Reachability and Ports

Check the open ports on your Defguard Edge server:

```
sudo nmap -Pn -sS edge.example.com
```

Expected output:

```
Host is up (0.0082s latency).
PORT    STATE  SERVICE
443/tcp open   https
```

Interpretation:

* The host is reachable from the Internet.
* Only port 443/tcp is open, as expected for HTTPS access.
* If you configured Edge to use automatic Let's Encrypt certificate issuance, port `80/tcp` will also be open. This is expected - Let's Encrypt requires it for domain validation. Verify that no other ports are open.

#### Test: Defguard Gateway Server Reachability

Check if the Defguard Gateway server is reachable:

```
sudo nmap -Pn -sS vpn.example.com
```

Expected output:

```
Host is up (0.0082s latency).
```

Interpretation:

* The host is reachable.
* The list of open TCP ports should be empty, as the Gateway primarily uses UDP for VPN connections.
* You’ll verify the UDP port functionality in the next step by testing an actual VPN connection.
* If you configured the optional `HEALTH_PORT` on your Gateway for monitoring purposes, one additional TCP port will appear as open. This is expected. Verify that only that port and no others are open.

#### Test: Internal management ports are not publicly reachable

Edge and Gateway each expose a gRPC port used exclusively for management connections from Core. These ports must not be reachable from the public Internet.

Check that Edge's gRPC port is not reachable:

```
sudo nmap -Pn -sS -p 50051 edge.example.com
```

Check that Gateway's gRPC port is not reachable:

```
sudo nmap -Pn -sS -p 50066 vpn.example.com
```

Expected output for both (with a corresponding port):

```
PORT      STATE    SERVICE                                                                                                                                                                                     
50051/tcp filtered unknown
```

Interpretation:

* A state of `filtered` or `closed` confirms the port is not reachable from the Internet, as required.
* If the port shows as `open`, your firewall rules are not correctly restricting access to the management port. Review your DMZ ingress rules and ensure only Core's internal IP is allowed to reach these ports.

### Connecting to the VPN

1. Open the Defguard Desktop Client.
2. Connect to your configured location.

#### Test: Verify VPN Connectivity

Once connected:

1.  Open your browser and navigate to the Defguard Core interface, for example:

    https://defguard.example.com
2. Sign in using an administrator account.

If you can access the web panel, your VPN connection is active and functioning.

Then, in the Core UI:

* Go to VPN Overview page. You should see your connected device listed there.

<figure><img src="../.gitbook/assets/image (1).png" alt="VPN Overview page showing a connected device"><figcaption><p>The VPN Overview page in the Core UI showing your workstation listed<br>as a connected device.</p></figcaption></figure>

### Testing While Connected to the VPN

This phase verifies VPN routing: once the tunnel is active, traffic should reach Core through the VPN, confirming the end-to-end setup is working correctly.

Perform the following tests again while the Defguard client remains connected.

In this state:

* ✅ You should be additionally able to reach the Defguard Core server.

#### Test: Defguard Core Server Reachability and Ports

Check the open ports on your Defguard Core server:

```
sudo nmap -Pn -sS defguard.example.com
```

Expected output:

```
Host is up (0.021s latency).
PORT    STATE  SERVICE
443/tcp open   https
```

Interpretation:

* The host is reachable via the VPN tunnel.
* Port 443/tcp (HTTPS web interface) is open, which confirms proper VPN routing and Core access.

## Summary

✅ Firewall policies match the expected inbound/outbound rules for each component.

✅ DNS records resolve to the expected internal and public addresses.

✅ Core is unreachable from the Internet and reachable only via VPN.

✅ Edge is publicly reachable on port 443 only; its gRPC management port (50051) is not reachable from the Internet.

✅ Gateway accepts VPN connections; its gRPC management port (50066) is not reachable from the Internet.

✅ Core can reach Edge and Gateway on their management ports.

✅ VPN connectivity is established and Core is accessible through the tunnel.

When all verifications and tests pass, your Defguard deployment is operational, properly segmented, and production-ready.
