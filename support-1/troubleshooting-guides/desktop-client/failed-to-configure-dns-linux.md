# Failed to configure DNS (Linux)

When a location has [DNS servers configured](../../../features/wireguard/dns-and-domains.md#changing-dns-settings), the Defguard client configures DNS for the VPN interface through one of two backends. It picks the first one that is available:

1. **`systemd-resolved`** - used when `systemd-resolved` is running and the `resolvectl` command is installed. This is the recommended backend and the only one that fully supports [split DNS](../../../features/wireguard/dns-and-domains.md).
2. **`resolvconf`** - used otherwise.

If neither is available, connecting fails with an error similar to `Failed to configure DNS for WireGuard interface ...: No supported DNS backend found`.

## Check which backend the client can use

Verify that `systemd-resolved` is running:

```sh
systemctl status systemd-resolved
resolvectl status
```

If `systemd-resolved` is running, the client uses it directly and does not need `resolvconf`. If it is running but `resolvectl` is missing, install your distribution's package that provides `resolvectl` (on Debian and Ubuntu, `systemd-resolved`).

If you do not use `systemd-resolved`, install a package that provides the `resolvconf` command, such as `openresolv`. See [resolvconf not found (Debian)](resolvconf-not-found-debian.md).

{% hint style="warning" %}
With `resolvconf`, split DNS works only when `resolvconf` passes DNS settings to a local resolver (`dnsmasq`, `unbound` or `pdnsd`). Without one, the VPN's DNS server is placed first and answers nearly all queries, and the client logs a warning that split DNS is not possible on this host.
{% endhint %}

## Check the applied configuration

While connected, show the DNS settings of the VPN interface (replace `<interface>` with the name from `ip -br link` or `sudo wg show interfaces`):

```sh
resolvectl status <interface>
```

With DNS servers and domains in the location (split DNS), the output looks like this:

```
Link 22 (wg1)
    Current Scopes: DNS
         Protocols: -DefaultRoute +LLMNR +mDNS -DNSOverTLS DNSSEC=no/unsupported
Current DNS Server: 10.0.0.53
       DNS Servers: 10.0.0.53
        DNS Domain: corp.example.com
     Default Route: no
```

`Default Route: no` means the interface only answers names under the listed domains.

With DNS servers only (full-tunnel DNS), you should see `DNS Domain: ~.` and `Default Route: yes` instead.

To see which interface answers a name, use:

```sh
resolvectl query my.internal.service.com
```

The last line shows the link that was used.

## DNS resolution check

If DNS servers are configured in the location but users cannot resolve internal hostnames, check the following:

1.  **Routing** - confirm requests to the network segments where your DNS servers reside are routed through the WireGuard interface:

    ```sh
    ip route
    ```
2.  **WireGuard allowed IPs** - confirm the DNS server network segments appear in the `allowed ips` list for the peer:

    ```sh
    sudo wg
    ```
3. **Domains** - with split DNS, only names under the domains listed in the location's DNS field go to the internal DNS server. Confirm the name you are resolving is under one of them.
4.  **Manual resolution test** - try resolving a name directly through one of your internal DNS servers:

    ```sh
    dig @DNS_SERVER_IP my.internal.service.com
    ```

    This bypasses the client's DNS settings and only confirms that the server is reachable and answers. Use `resolvectl query` to test what applications will see.
