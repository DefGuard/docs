---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/wireguard/dns-and-domains
---

# DNS and domains

When a client connects to a location, Defguard can push DNS settings to it so that name resolution
happens through the VPN tunnel. This lets clients resolve internal hostnames (for example
`server.internal`) and, when a search domain is configured, use short names instead of fully
qualified domain names.

Two settings control this behaviour:

* **DNS** – one or more DNS server IP addresses the client should use while the tunnel is up.
  Separate multiple addresses with commas (`,`). These are typically your internal resolvers, so
  private zones resolve correctly.
* **Search domain** – a domain that is automatically appended to unqualified hostnames. For example,
  with a search domain of `internal`, resolving `server` is treated as `server.internal`.

## Changing DNS settings

To add or change DNS settings or a DNS search domain:

* Go to **Locations**.
* From the action menu select **Edit**.
* In the **Internal VPN** section you can modify the **DNS** field. Enter the IP addresses of DNS
  servers (separated by commas `,`) and a search domain.
* Save the changes.

The new settings are applied to clients the next time they connect (or reconnect) to the location.

For example:

<figure><img src="../../.gitbook/assets/image (317).png"><figcaption></figcaption></figure>

{% hint style="info" %}
**The DNS server's IP address must be reachable through the tunnel.**

For DNS queries to actually travel over the VPN, the DNS server's IP address has to fall within the
location's **Allowed IPs**. Allowed IPs define which destinations are routed into the tunnel, so if
the DNS server is not covered by them, one of two things happens:

* **DNS leak** – if the client has another route to reach that IP (for example over the local
  network), queries are sent outside the tunnel. Your lookups are then visible to whoever controls
  that path, and internal-only zones may fail to resolve.
* **Broken resolution** – if there is no alternate path to the DNS server, name resolution stops
  working entirely while the tunnel is up, which can make the connection appear "dead" even though
  the tunnel itself is healthy.

To avoid both, make sure the subnet containing your DNS server is included in **Allowed IPs**. If you
route all traffic through the tunnel (Allowed IPs set to `0.0.0.0/0, ::/0`) this is already covered.
{% endhint %}
