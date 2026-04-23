# How to verify the VPN is working

After connecting to a VPN location, you should be able to ping the gateway server's VPN IP address. For example, if your location is configured with the network `10.1.1.0/24` and the gateway IP is `10.1.1.1`, run:

```bash
ping 10.1.1.1
```

If that succeeds, the tunnel is up and traffic is reaching the gateway. If it does not, work through the sections below.

### VPN location settings changed

If the location configuration changed after the client enrolled (for example, new DNS servers, changed allowed IPs, or a modified network range), the client may be operating with outdated configuration. Update the instance configuration manually in the desktop client by navigating to the instance and selecting **Update**.

On Enterprise installations, configuration changes sync to clients automatically.

### Conflicting networks

A common cause of failed pings to the VPN gateway is a routing conflict. If you have a local network or ISP-assigned network that overlaps with the VPN network, the operating system routes VPN-bound traffic through the wrong interface.

Check your routing table:

```bash
# Linux / macOS
netstat -rn
```

Look for any route that covers the VPN network range. For example, if the VPN network is `10.1.1.0/24` but you have a local route for `10.0.0.0/8` going through your physical interface, the VPN packets will never reach the tunnel.

The fix is to choose a non-conflicting VPN address range when configuring the location.

### Firewall rules on the gateway server

The server running the gateway may have firewall rules that block incoming WireGuard traffic. Check both `ufw` and `iptables`:

```bash
sudo ufw status
sudo iptables -L -n
```

Note that `iptables` rules can persist even when `ufw` is disabled or shows as inactive. Review both independently.

WireGuard uses UDP. Make sure the WireGuard port for your location (default: `51820`) is open on the gateway server.

### Access control lists

Check whether the user's device is allowed to access the location. Open the location in the Defguard web UI and review the **Access control** settings. If the location is restricted to specific groups, confirm the user is a member of the required group.
