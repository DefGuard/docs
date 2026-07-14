# resolvconf not found (Debian)

On some Linux distributions, including Debian 12 and 13, the Defguard client may fail to establish a VPN tunnel after clicking Connect. The root cause is that the `resolvconf` command is not installed.

Install it with:

```sh
sudo apt install openresolv
```

Retry the connection after installation.
