# General troubleshooting

### Failed to configure DNS (Linux)

This error commonly occurs on Ubuntu 22. Defguard client internally calls `resolvconf` to set DNS servers. The only tested backend is `systemd-resolved`, so make sure you use it before proceeding further (`systemctl status systemd-resolved`).&#x20;

On newer Ubuntu distributions (23 and up) `resolvconf` is, by default, a symbolic link to `resolvectl` and this is the recommended way of interacting with the system's DNS configuration. On Ubuntu 22 the symbolic link doesn't exist and the most straightforward way to fix this issue is to manually create it:

```bash
sudo ln -s /usr/bin/resolvectl /usr/sbin/resolvconf
```

If this fails, one may also try installing one of the packages providing the `resolvconf` command, like `openresolv` or `resolvconf` but this has not been tested and may possibly cause issues with `systemd-resolved`, so proceed at your own risk.
