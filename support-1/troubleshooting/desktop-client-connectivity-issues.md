# Desktop Client connectivity issues

## Overview

This guide provides instructions for troubleshooting common network connectivity issues that can be encountered when using WireGuard VPN clients to access VPN-protected resources.

## Preparing for testing

Before beginning connectivity tests, ensure:

* Disable ACLs and MFA for the VPN location being tested
* Temporarily disable firewall rules dropping ICMP traffic on the gateway server

{% hint style="warning" %}
Remember to restore the original settings after you conclude your testing.
{% endhint %}

This way we remove additional factors and can check if the basic VPN connection is configured correctly.

## VPN Gateway Public IP

First, verify basic network connectivity to the gateway's public IP:

```bash
ping <GATEWAY_PUBLIC_IP>
```

**Troubleshooting if IP is unreachable:**

* Verify the IP address is correctly configured in [location settings](../../features/wireguard/create-your-vpn-network.md#gateway-address)
* Verify your gateway-side firewall rules don't block traffic from the internet
* Check client-side firewall rules if you use them

## VPN Connection Test

If the public IP is reachable, attempt to connect using the VPN client:

* Initiate connection to the VPN location
* If errors occur, check [client logs](../../using-defguard-for-end-users/desktop-client/#log-files) for detailed information

## VPN Network Connectivity

Once connected to a VPN location, test internal VPN connectivity by trying to ping the Gateway internal IP configured in [location settings](../../features/wireguard/create-your-vpn-network.md#gateway-vpn-ip-addresses-and-masks):

```bash
ping <GATEWAY_VPN_SUBNET_IP>
```

**Example:** If your VPN subnet is `10.0.10.0/24`, the gateway is typically `10.0.10.1`&#x20;

If you have the official [WireGuard CLI](https://www.wireguard.com/install/) installed you can also verify the VPN connection by checking for the latest handshake. If there's no handshake, a connection has not been established.<br>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**If gateway is unreachable within VPN subnet:**

* This indicates WireGuard connection failed to establish
* Client will automatically disconnect after 5 minutes (300 seconds) if no WireGuard traffic is detected (this threshold is configurable in client settings)
* This might indicate that the VPN server is not allowing UDP traffic on the [specified port](../../features/wireguard/create-your-vpn-network.md#gateway-port)
* Check if the firewall is [configured correctly](../../deployment-strategies/hardware-os-network-and-firewall-recommendations.md#gateway) to allow UDP traffic on the selected port

### Verify UDP Port Connectivity

If  you want to explicitly test if UDP traffic is allowed between the client and VPN server you can do the following:

*   start a test UDP server on the gateway server:

    ```bash
    socat UDP-RECV:<UDP_PORT> -
    ```
*   send a message from a client machine:

    ```bash
     echo "Hello UDP test" | socat - UDP:<GW_PUBLIC_IP>:<UDP_PORT>
    ```
* if UDP traffic is allowed, a message should appear in your server terminal

### Loss of connection after network change

The connection to the VPN network may stop working if your internet network has changed while the connection was active (for example if you switched Wi-Fis). This can be resolved by simply re-connecting to a given VPN location.

## DNS Configuration Issues

If the VPN connection establishes, but you still cannot access VPN-protected resources it can sometimes mean there's an issue with the [DNS server](../../features/wireguard/create-your-vpn-network.md#dns) configured for a given VPN location.

**To verify DNS functionality:**

* Check if DNS configuration in location settings is correct
* Test DNS resolution for the resources you're trying to access

**DNS Testing Commands:**

**Linux:**

```bash
# Test DNS resolution
nslookup <resource-domain>
dig <resource-domain>

# Check which DNS servers are being used
systemd-resolve --status
cat /etc/resolv.conf
```

**macOS:**

```bash
# Test DNS resolution
nslookup <resource-domain>
dig <resource-domain>

# Check DNS configuration
scutil --dns
```

**Windows:**

```powershell
# Test DNS resolution
nslookup <resource-domain>

# Check DNS configuration
ipconfig /all

# Flush DNS cache (if needed)
ipconfig /flushdns
```

**Troubleshooting DNS Issues:**

* Verify DNS server IP addresses in location settings
* Test with different DNS servers (e.g., 8.8.8.8, 1.1.1.1)
* Verify there are no firewall rules on your gateway server which would block DNS access

## MTU Issues

If VPN connects but you cannot access certain resources, another possible cause is an invalid MTU configuration of the network interface.

By default WireGuard uses an MTU of 1420 bytes, but some ISPs may limit packet size.

**To test MTU limits:**\
**Linux:**

```bash
ping -M do -s 1420 <GATEWAY_PUBLIC_IP>
```

**Windows:**

```powershell
ping -f -l 1420 <GATEWAY_PUBLIC_IP>
```

You can try to lower the packet size until the ping command succeeds to find the limit.

**Workaround:**

* Set a lower MTU value manually for the WireGuard interface created by the client
