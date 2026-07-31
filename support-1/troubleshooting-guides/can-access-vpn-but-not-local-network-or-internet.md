# Can access VPN but not local network or internet

Defguard configures the WireGuard interface and peers. What happens to traffic after it enters the gateway server is determined by the server's network configuration, not by Defguard itself.

Two common setups after the tunnel is established:

**Static routing**

Add routing rules on the gateway server so that traffic arriving from the VPN IP range is forwarded to your internal network. With this approach, clients appear in the internal network with their VPN-assigned IP.

**Masquerade / NAT**

For internet access through the VPN, or when the upstream network has no route back to the VPN subnet, configure masquerading or NAT on the gateway server. Example for `iptables`:

```sh
iptables -t nat -A POSTROUTING -s 10.1.1.0/24 -o eth0 -j MASQUERADE
```

Replace `10.1.1.0/24` with your VPN network range and `eth0` with your external interface.

If you run Defguard Gateway directly, you can also enable automatic source NAT with `--masquerade` or `DEFGUARD_MASQUERADE=true`. This is the quickest option for simple internet-egress setups.

Also enable IP forwarding if it is not already active:

```sh
echo 1 > /proc/sys/net/ipv4/ip_forward
```

To persist across reboots, add `net.ipv4.ip_forward = 1` to `/etc/sysctl.conf`.

{% hint style="warning" %}
On hosts that also run Docker, Docker may set the `FORWARD` policy to `drop`. In that case WireGuard traffic can be dropped before it reaches `POSTROUTING`, so a correct masquerade rule still shows little or no traffic.

Allow WireGuard traffic early in Docker's `DOCKER-USER` chain:

```sh
iptables -I DOCKER-USER -i wg0 -j ACCEPT
iptables -I DOCKER-USER -o wg0 -j ACCEPT
```

Replace `wg0` if your Gateway uses a different interface name.

These rules are not persistent by default. On Debian or Ubuntu, install `iptables-persistent` and save the current rules:

```sh
apt install iptables-persistent
netfilter-persistent save
```
{% endhint %}
