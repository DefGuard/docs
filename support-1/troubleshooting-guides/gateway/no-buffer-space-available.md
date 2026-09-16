# No buffer space available

Log entry:

```
ERROR defguard_gateway::gateway Failed to update network configuration: Firewall error: Netlink error: Failed while reading a message from socket: Os { code: 105, kind: Uncategorized, message: "No buffer space available" }
```

This error means the kernel socket receive/send buffers are too small for the volume of netlink messages the gateway is generating. This typically occurs on gateways with many peers or high peer churn.

### Fix

Increase the kernel socket buffer sizes. Add the following to `/etc/sysctl.conf`:

```ini
net.core.rmem_max = 67108864
net.core.wmem_max = 67108864
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_wmem = 4096 65536 67108864
net.core.rmem_default = 524288
net.core.wmem_default = 524288
```

Apply without rebooting:

```sh
sudo sysctl -p
```

Restart the gateway service after applying:

```sh
sudo systemctl restart defguard-gateway
```

More context: [github.com/DefGuard/defguard/issues/1303](https://github.com/DefGuard/defguard/issues/1303)
