# resolvconf not found (Debian)

On some Linux distributions, including Debian 12 and 13, the Defguard client may fail to establish a VPN tunnel to a location with DNS servers configured. This happens when `systemd-resolved` is not running and the `resolvconf` command is not installed, so the client has no way to configure DNS.

Install a package that provides `resolvconf`:

```sh
sudo apt install openresolv
```

Retry the connection after installation.

{% hint style="info" %}
With `resolvconf` and no local resolver such as `dnsmasq`, the client cannot do [split DNS](../../../features/wireguard/dns-and-domains.md): the VPN's DNS server answers nearly all queries. For split DNS, use `systemd-resolved` instead:

```sh
sudo apt install systemd-resolved
sudo systemctl enable --now systemd-resolved
```

This changes name resolution for the whole machine: `/etc/resolv.conf` is replaced with a link to the `systemd-resolved` local stub resolver. Check that your existing DNS setup works with it before switching. See [Failed to configure DNS (Linux)](failed-to-configure-dns-linux.md).
{% endhint %}

