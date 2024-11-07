# High Availability and Failover

Currently we support the following HA/failover scenarios:

### Gateway - High Availability

We support active-active configurations with multiple gateways for a single VPN instance or location. Since our gateway uses a vanilla kernel WireGuard®, there are multiple approaches for implementation.&#x20;

We recommend to have a floating public IPs between those gateways, but we don’t enforce a specific approach, as each operating system and administrator may have different preferences.

### Core / Proxy - Failover

The core service handles gateway states as well as core connects _**to the proxy**_. Since proxy serves HTTP based protocol communication and should be in the public Internet, it needs to be secure, thus core connects to the proxy.

This way **core can be in an Intranet network segment and proxy can be in DMZ, making Core  completely cut-off on firewall from the Internet** (you only can have only outgoing firewall rules from Intranet allowing only for core to connect to proxy).

So **High Availability for core and proxy** gets complicated, with multiple proxies core needs to manage those connections. We already have most of the code for that ready, but it's not yet production ready.

How to bulet-proof proxy & core then?

We recommend to deploy them on a failover solution - like on a kubernetes cluster (even small one - like mini-kube) . This way, kubernetes manages: healthecks and does failover. You can have cluster N-nodes and if any VM/node with Core/Proxy goes offline or health checks fail - it's migrated to a new node.

