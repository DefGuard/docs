# DNS and domains

When a client connects to a location, Defguard can push DNS settings to it. This functionality can be used in two ways:

* **Split DNS** - only your internal domains (for example `corp.example.com`) are resolved by your internal DNS server through the VPN. Every other name, such as `google.com`, is resolved by the DNS the device already uses.
* **Full-tunnel DNS** - every DNS query from the device goes to your DNS server through the VPN.

Which one you get depends only on what you enter in the location configuration's **DNS** field.

## The DNS field

The **DNS** field takes a comma-separated list of entries. Each entry is either:

* a **DNS server** IP address (IPv4 or IPv6), or
* a **domain**, such as `corp.example.com`.

Clients sort the entries by type, so their order only matters among the domains (see [Multiple domains](dns-and-domains.md#multiple-domains)).

| DNS field                                                 | Result                                                                                                    |
| --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `10.0.0.53`                                               | Full-tunnel DNS: all queries go to `10.0.0.53`.                                                           |
| `10.0.0.53, corp.example.com`                             | Split DNS: names under `corp.example.com` go to `10.0.0.53`, all other names use the device's normal DNS. |
| `10.0.0.53, 10.0.0.54, corp.example.com, lab.example.net` | Split DNS for both domains, using either server.                                                          |
| `corp.example.com` (no IP address)                        | Desktop and iOS clients ignore it and leave DNS settings unchanged. Always include at least one server.   |

{% hint style="warning" %}
Split DNS is not available on every platform. See [Platform support](dns-and-domains.md#platform-support).
{% endhint %}

Every domain you enter has two effects:

* **Routing** - queries for that domain and all its subdomains go to the location's DNS servers.
* **Search suffix** - short names are completed with it. With `corp.example.com` listed, looking up `wiki` is resolved as `wiki.corp.example.com`, through the VPN.

#### Limitations

* Currently a domain cannot be used only for routing or only as a search domain. It's always used as both.
* All DNS servers in the field serve all domains in the field. You cannot map different domains to different servers.

## Changing DNS settings

* Go to **Locations**.
* From the action menu select **Edit**.
* In the **Internal VPN** section, edit the [**DNS**](dns-and-domains.md#the-dns-field) field.
* Save the changes.

Clients apply the [new settings](../remote-user-enrollment/automatic-real-time-desktop-client-configuration.md) the next time they connect (or reconnect) to the location.

{% hint style="info" %}
**The DNS server's IP address must be reachable through the tunnel.**

For DNS queries to actually travel over the VPN, the DNS server's IP address has to fall within the location's **Allowed IPs**. Allowed IPs define which destinations are routed into the tunnel, so if the DNS server is not covered by them, one of two things happens:

* **DNS leak** - if the client has another route to reach that IP (for example over the local network), queries are sent outside the tunnel. Your lookups are then visible to whoever controls that path, and internal-only zones may fail to resolve.
* **Broken resolution** - if there is no alternate path to the DNS server, name resolution stops working entirely while the tunnel is up, which can make the connection appear "dead" even though the tunnel itself is healthy.

To avoid both, make sure the subnet containing your DNS server is included in **Allowed IPs**. If you route all traffic through the tunnel (Allowed IPs set to `0.0.0.0/0, ::/0`) this is already covered. This applies to split DNS too: the queries for your internal domains must reach the server through the tunnel.
{% endhint %}

### Platform support

Split DNS depends on what the operating system allows a VPN to do.

| Platform                                  | Split DNS | Notes                                                                                                                                                                                                                                                                           |
| ----------------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Linux with `systemd-resolved`             | Yes       | Desktop client 2.2 or later.                                                                                                                                                                                                                                                    |
| Linux without `systemd-resolved`          | Limited   | Works only if `resolvconf` drives a local resolver (`dnsmasq`, `unbound` or `pdnsd`). Otherwise the VPN's DNS server answers nearly all queries. See [Failed to configure DNS (Linux)](../../support-1/troubleshooting-guides/desktop-client/failed-to-configure-dns-linux.md). |
| Windows                                   | Yes       | Desktop client 2.2 or later. Uses Name Resolution Policy Table (NRPT) rules, which `Get-DnsClientNrptPolicy` lists while connected.                                                                                                                                             |
| macOS                                     | Yes       | Desktop client 2.1 or later.                                                                                                                                                                                                                                                    |
| iOS                                       | Yes       | Mobile client 1.7.0 or later.                                                                                                                                                                                                                                                   |
| Android                                   | No        | Android sends all DNS queries to the VPN's DNS servers whenever a VPN sets any. Domains still work as search suffixes.                                                                                                                                                          |
| Other WireGuard apps (downloaded `.conf`) | No        | The DNS field is written verbatim as `DNS = ...`. Tools such as `wg-quick` send all queries to the listed servers and use the domains only as search suffixes.                                                                                                                  |

On platforms without split DNS, make sure your DNS server can resolve public names as well as internal ones.

## Choosing domains

{% hint style="warning" %}
**List the narrowest internal zones, not your organisation's main domain.**

A domain routes all of its subdomains. If you enter `example.com` while `www.example.com` is a public site, every `example.com` name is sent to your internal server while the VPN is connected. If that server does not answer for public `example.com` names, they stop resolving. Prefer `corp.example.com` or `internal.example.com` if possible.
{% endhint %}

### Multiple domains

You can list several domains. Each is routed to the location's DNS servers. For a short name, the device tries each domain as a suffix in the order you entered them and uses the first name that exists. Put the most used domain first.

### Multiple connected locations

Each connected location's domains are routed to that location's DNS servers. When two domains overlap, the more specific one wins: `a.corp.example.com` goes to a location listing `corp.example.com` rather than one listing `example.com`.

Do not list the same domain in two locations that users connect to at the same time. Which location then answers the query depends on the operating system.
