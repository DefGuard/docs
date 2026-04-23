# Can access VPN but not local network or internet

Defguard configures the WireGuard interface and peers. What happens to traffic after it enters the gateway server is determined by the server's network configuration, not by Defguard itself.

Two common setups after the tunnel is established:

**Static routing**

Add routing rules on the gateway server so that traffic arriving from the VPN IP range is forwarded to your internal network. With this approach, clients appear in the internal network with their VPN-assigned IP.

**Masquerade / NAT**

For internet access through the VPN, or when you want to hide client IPs, configure masquerading or NAT on the gateway server. Example for `iptables`:

```bash
iptables -t nat -A POSTROUTING -s 10.1.1.0/24 -o eth0 -j MASQUERADE
```

Replace `10.1.1.0/24` with your VPN network range and `eth0` with your external interface.

Also enable IP forwarding if it is not already active:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

To persist across reboots, add `net.ipv4.ip_forward = 1` to `/etc/sysctl.conf`.

A full step-by-step example for enabling internet access through the VPN is available in the [deployment tutorial](https://docs.defguard.net/2.0/tutorials/step-by-step-setting-up-a-vpn-server#enabling-to-access-internet-through-your-vpn).
