---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/high-availability-and-failover
---

# High Availability and Failover

## Gateway - High Availability

We support running multiple gateways within a single VPN location to achieve high availability. This configuration requires an Enterprise [Broken link](/broken/pages/vEBaL5Pdry1nZO8V50bh "mention").

### Deploying multiple gateways for one location

Multiple gateways can be added by using the "Gateway setup" context menu in the "Locations" table

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Each gateway deployed for a given location will receive the same network configuration and will **bind to the defined port** in the location's _Gateway Port._

The only thing left to do is to point your traffic to those gateways, which can be accomplished in several ways:

{% hint style="warning" %}
WireGuard is a stateful UDP protocol, so once a client establishes a session with a specific gateway, only that gateway can decrypt its traffic. In a multi-gateway setup, you must use a Layer 4 load balancer with properly configured **health checks** and **sticky sessions** to ensure traffic is consistently routed to a healthy instance and automatically redirected if a gateway fails.

You can find example Envoy proxy configuration in our [deployment repository](https://github.com/DefGuard/deployment/tree/main/docker-compose2.0).

More details regarding WireGuard load-balancing can be found here: [wireguard-udp-load-balancing.md](wireguard-udp-load-balancing.md "mention").
{% endhint %}

* **Proxy/load balancing** - The load balancer must be configured with Gateway addresses and ports. Clients connect to the proxy, which forwards VPN (UDP) traffic to one of the gateway backends. The proxy must perform regular health checks and ensure that traffic from each client is consistently routed to a single gateway instance (sticky sessions).
* **Floating public IP** - In this scenario, the public IP configured as the Location Gateway Address is assigned to only one gateway at a time. If the active gateway becomes unavailable, the floating IP automatically moves to the standby gateway, ensuring that client traffic is redirected to the healthy server.

Example configuration can be found in [our deployment repository](https://github.com/DefGuard/deployment/blob/main/docker-compose2.0/docker-compose.ha.yaml).

### Determining if multiple gateways are running

All gateways successfully connected to a location are displayed under the Location in VPN Overview and locations table:

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

### What is the gateway peers persistence (if core/proxy services fail)

1. For **VPN Locations without MFA** - it's persistent until system reboot - _even if the gateway does not work_ - as the gateway configures WireGuard "in kernel".
2. For **VPN Locations with MFA**, this depends on the _Peer Disconnect Threshold (seconds)_ setting in the VPN Location settings. This setting specifies that if the peer is inactive for _(defined seconds)_, the gateway should remove it from the configuration. Therefore, if the proxy/core is not operational, MFA authentication will fail, and the peer will not be added if it is disconnected.

## Edge High Availability

Edge is a stateless service, which makes it straightforward to run multiple instances for high availability. Because request handling is not tied to local application state, Edge instances can be placed behind a standard reverse proxy or load balancer such as NGINX to distribute traffic and provide failover. In this setup, the load balancer handles incoming connections and forwards them to any healthy Edge instance, allowing the service to remain available even if one instance goes down. When deploying Edge in this way, make sure each instance has consistent runtime configuration and access to the required certificate material.

Example configuration can be found in [our deployment repository](https://github.com/DefGuard/deployment/blob/main/docker-compose2.0/docker-compose.ha.yaml).

## Core Failover

The Core service manages Gateway state and communicates with the Edge. Because the Edge exposes HTTP-based protocols and is intended to be publicly accessible, it must be secured, so the Core connects to the Edge.

This way **core can be in an Intranet network segment and proxy can be in DMZ, making Core completely cut-off on firewall from the Internet** (you can have only outgoing firewall rules from Intranet allowing only for core to connect to proxy).

Defguard Core is not yet a fully stateless service, which means it is not currently designed for simple horizontal scaling behind a standard load balancer in the same way as Edge. While Core can be deployed reliably as a single central service, running multiple active Core instances requires additional care and is not yet a generally supported high-availability model. Work toward a fully load-balanced Core architecture is ongoing and is expected to become available in future Defguard versions.

#### How to bullet-proof core with failover?

We recommend to deploy them on a failover solution - like on a kubernetes cluster (even small one - like mini-kube) . This way, Kubernetes manages: healthchecks and does failover. You can have cluster N-nodes and if any VM/node with Core/Proxy goes offline or health checks fail - it's migrated to a new node.
