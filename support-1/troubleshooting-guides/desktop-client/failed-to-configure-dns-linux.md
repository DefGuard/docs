# Failed to configure DNS (Linux)

This error commonly appears on Ubuntu 22. The Defguard client uses `resolvconf` internally to configure DNS servers on the system. The only tested and supported backend is `systemd-resolved`.

Before proceeding, verify that `systemd-resolved` is running:

```bash
systemctl status systemd-resolved
```

### Ubuntu 22

On Ubuntu 23 and later, `resolvconf` is a symbolic link to `resolvectl` by default. On Ubuntu 22 this link does not exist. The straightforward fix is to create it manually:

```bash
sudo ln -s /usr/bin/resolvectl /usr/sbin/resolvconf
```

If that fails, you can also try installing a package that provides the `resolvconf` command, such as `openresolv`. Note that using `openresolv` alongside `systemd-resolved` has not been tested and may cause conflicts — proceed with caution.

### DNS resolution check

If DNS servers are configured in the location but users cannot resolve internal hostnames, check the following:

1.  **Routing** — confirm requests to the network segments where your DNS servers reside are routed through the WireGuard interface:

    ```bash
    ip route
    ```
2.  **WireGuard allowed IPs** — confirm the DNS server network segments appear in the `allowed ips` list for the peer:

    ```bash
    sudo wg
    ```
3.  **Manual resolution test** — try resolving a name directly through one of your internal DNS servers:

    ```bash
    dig @DNS_SERVER_IP my.internal.service.com
    ```
