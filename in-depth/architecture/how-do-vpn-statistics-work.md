# How do VPN statistics work

1. Each Defguard Gateway reads WireGuard® interface data for each peer.
2. If any change is detected between previous stats (number of bytes transferred in/out the network interface) then, through the gRPC interface, Gateway sends those statistics to Defguard Core.

{% hint style="info" %}
If the Gateway doesn’t detect any changes in stats, then it doesn’t send anything to Core.

The period for each Gateway to gather stats from the interfaces is defined by `DEFGUARD_STATS_PERIOD` ENV value (or `-p` argument). The default value is set to 30 seconds.
{% endhint %}

3. Core stores all the data received through gRPC in the database (table `wireguard_peer_stats`).
4. Then, the VPN overview does all the calculations and aggregations of the data to present it.
