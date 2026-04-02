---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/in-depth/architecture/how-do-vpn-statistics-work
---

# How do VPN statistics work

1. Each Defguard gateway reads kernel data from the WireGuard® interface for each peer.
2. If any change is detected between previous stats (bytes in/out) then through the gRPC interface the gateway sends those statistics to Defguard core.

{% hint style="info" %}
If the gateway doesn't detect any changes in stats, doesn't send anything to core.

The period for each gateway to gather stats from the interfaces is defined by `DEFGUARD_STATS_PERIOD` ENV value (or -p argument) - default 30sec.
{% endhint %}

3. Core stores all the data send trough gRPC in the database (table `wireguard_peer_stats`).
4. Then when displaying the VPN overview does all the calculations and aggregations of the data to display them.
