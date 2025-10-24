# Production deployment verification guide

This guide helps you verify that your Defguard instance is operational, reachable through the expected network paths, and properly secured.

The process will consist of the following steps:

1. Verifying the configuration of your firewall rules.
2. Verifying your DNS resolution.
3. Testing the whole configuration.

## Prerequisites

Before proceeding, ensure that you deployed your Defguard environment according to the [recommendations](hardware-os-network-and-firewall-recommendations.md) and that the following components are operational:

* 1 server running Defguard Core
  * Located in an internal network segment (not exposed to the Internet)
  * Reachable internally under a domain such as defguard.example.com
* 1 server running Defguard Proxy
  * Located in a DMZ network segment
  * Publicly accessible from the Internet under a domain such as enrolment.example.com
* 1 server running Defguard Gateway
  * Located in a DMZ network segment
  * Publicly accessible from the Internet under a domain such as vpn.example.com
* A workstation with the Defguard Desktop Client installed and configured for VPN connection testing.

## Verify firewall policies

Confirm that your firewall rules align with Defguard’s secure deployment model.

| Component | Allowed inbound                                                      | Blocked inbound           | Notes                                                  |
| --------- | -------------------------------------------------------------------- | ------------------------- | ------------------------------------------------------ |
| Core      | TCP 443 (from internal/VPN only)  gRPC port (from Proxy and Gateway) | All public traffic        | Core should never be directly exposed to the Internet. |
| Proxy     | TCP 443 (from public Internet)  gRPC port (from Core)                | All other inbound traffic | Used for enrolment and client configuration.           |
| Gateway   | UDP VPN port (e.g. 50555)  gRPC port (from Core)                     | All other inbound traffic | Only VPN and Core communication should be allowed.     |

## Verify DNS resolution

Proper DNS configuration ensures that each Defguard component resolves to the correct IP address and network zone.

Run:

```
dig +short vpn.example.com
dig +short enrolment.example.com
dig +short defguard.example.com
```

Expected results:

| Domain                | Expected IP Type    | Description                                            |
| --------------------- | ------------------- | ------------------------------------------------------ |
| vpn.example.com       | Public IP           | Gateway server reachable from the Internet             |
| enrolment.example.com | Public IP           | Proxy server for enrolment and configuration           |
| defguard.example.com  | Private/Internal IP | Core server, accessible only from internal/VPN network |

## Test the environment

After you've confirmed the proper network segmentation it's time to test it.

### Testing while disconnected from the VPN

Perform the following tests from the workstation where the Defguard Desktop Client is installed.

Make sure the client is disconnected before running any commands.

In this state:

* ❌ You should not be able to reach the Defguard Core server.
* ✅ You should be able to reach the Defguard Proxy server.
* ✅ You should be able to reach the Defguard Gateway server (UDP port for VPN).

#### Test: Defguard Core server reachability and ports

Check the open ports on your Defguard Core server (replace the example domain with your actual one):

```
sudo nmap -Pn -sS defguard.example.com
```

Expected output:

```
Failed to resolve "defguard.example.com".
```

Interpretation:

* The Core server is not reachable when disconnected from the VPN, which is the expected and secure configuration.

#### Test: Defguard Proxy Server Reachability and Ports

Check the open ports on your Defguard Proxy server:

```
sudo nmap -Pn -sS enrolment.example.com
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

<figure><img src="../.gitbook/assets/Screenshot 2025-10-24 at 11.20.24.png" alt=""><figcaption></figcaption></figure>

### Testing While Connected to the VPN

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

✅ Firewall policies restrict traffic to approved ports.

✅ DNS records resolve to the expected internal and public addresses.

✅ Core is unreachable from the Internet and reachable only via VPN.

✅ Proxy is publicly reachable only on port 443.

✅ Gateway responds correctly and allows VPN connections.

When all verifications and tests pass, your Defguard deployment is operational, properly segmented, and production-ready.
