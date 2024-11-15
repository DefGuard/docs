---
icon: bolt
---

# High Availability and Failover

Currently we support the following HA/failover scenarios:

## Gateway - High Availability

We support active-active configurations with multiple gateways for a single VPN instance or location. Since our gateway uses a vanilla kernel WireGuard®, there are multiple approaches for implementation.&#x20;

#### Deploying the active-active gateway setup

To have multi-gateway for one location setup, you need to [deploy the gateway on each server](gateway/).

If you already have a gateway deployed, and want to add new gateways for the location, go to _VPN Overview_ -> Click: _Edit Location Settings (in top right corner)_, then choose the location you want to add the gateway, and follow instructions for deployment:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-12 at 16.55.55.png" alt=""><figcaption></figcaption></figure>

After each gateway deployment all gateway will have the same configuration and will **bind to the defined port** in the location _Gateway Port._

The only thing left to do is to point your traffic to those gateway, which can be acomplished with various HA scenarios:

* floating public IP - if you choose this scenario, please remember that the IP must be the IP specified in the Location _Gateway Address_
* proxy/load balancing - also remember that the proxy must be configured with the _Gateway Address and Gateway Port_

### Determining if multiple gateways are running

All gateways that are successfully connected for the location are displayed under the Location in VPN Overview, here is an example for two gateways:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-12 at 17.01.42.png" alt=""><figcaption></figcaption></figure>

## Core / Proxy - Failover

The core service handles gateway states as well as core connects _**to the proxy**_. Since proxy serves HTTP based protocol communication and should be in the public Internet, it needs to be secure, thus core connects to the proxy.

This way **core can be in an Intranet network segment and proxy can be in DMZ, making Core  completely cut-off on firewall from the Internet** (you only can have only outgoing firewall rules from Intranet allowing only for core to connect to proxy).

So **High Availability for core and proxy** gets complicated, with multiple proxies core needs to manage those connections. We already have most of the code for that ready, but it's not yet production ready.

#### How to bulet-proof proxy & core with failover?

We recommend to deploy them on a failover solution - like on a kubernetes cluster (even small one - like mini-kube) . This way, kubernetes manages: healthecks and does failover. You can have cluster N-nodes and if any VM/node with Core/Proxy goes offline or health checks fail - it's migrated to a new node.

