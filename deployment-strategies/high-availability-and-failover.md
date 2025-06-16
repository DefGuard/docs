# High Availability and Failover

## Gateway - High Availability

We support running multiple gateways for a single VPN instance or location, enabling active-passive configurations. Active-active configurations should also be possible but come with some caveats. Since our gateway uses a vanilla kernel WireGuard®, there are multiple approaches for implementation.

{% hint style="info" %}
Please also see documentation of [Creating a New VPN location](../admin-and-features/wireguard/create-your-vpn-network.md) where each [location setting has information regarding high-availability](../admin-and-features/wireguard/create-your-vpn-network.md#vpn-location-settings).
{% endhint %}

#### Deploying multiple gateways for one location

To have a multi-gateway setup for a given location, you  will need to [deploy the gateway on each one of your servers](gateway/) under the same location.

If you already have a gateway deployed and want to add another one for the location, go to _VPN Overview_ -> Click: _Edit Location Settings (in the top right corner)_, then choose the location you want to add the new gateway to, and follow the deployment instructions:

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 16.55.55.png" alt=""><figcaption></figcaption></figure>

Each gateway deployed for a given location will receive the same network configuration and will **bind to the defined port** in the location's _Gateway Port._

The only thing left to do is to point your traffic to those gateways, which can be accomplished in several ways:

* Floating public IP - if you choose this scenario, please remember that the IP must be the IP specified in the Location _Gateway Address._ In this scenario, the floating IP switches between your gateway servers, directing the traffic to one of the two gateways.
* Proxy/load balancing - also remember that the proxy must be configured with the _Gateway Address and Gateway Port._ In this scenario, your clients connect to the proxy/load balancer, which direct the VPN traffic (UDP) to one of your gateway backend&#x73;_._

#### Active-active setups

Active-active setups should also be possible but come with some caveats. Here are the currently known issues with such configurations:

* Multiple running gateways bound to one location with network traffic distributed between them may produce invalid network usage statistics, making the network usage graphs and displays on the dashboard unreliable. Related issue: [https://github.com/DefGuard/defguard/issues/1022](https://github.com/DefGuard/defguard/issues/1022)

### Determining if multiple gateways are running

All gateways that are successfully connected for the location are displayed under the Location in VPN Overview, here is an example for two gateways:

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 17.01.42.png" alt=""><figcaption></figcaption></figure>

### What is the gateway peers persistence (if core/proxy services fail)

1. For **VPN Locations without MFA** - it's persistent until the system reboot - _even if the gateway will not work_ - as the gateway configures WireGuard "in kernel".
2. For **VPN Locations with MFA**, this depends on the _Peer Disconnect Threshold (seconds)_ setting in the VPN Location settings. This setting specifies that if the peer is inactive for _(defined seconds)_, the gateway should remove it from the configuration. Therefore, if the proxy/core is not operational, MFA authentication will fail, and the peer will not be added if it is disconnected.

## Core / Proxy - Failover

The core service handles gateway states as well as core connects _**to the proxy**_. Since proxy serves HTTP based protocol communication and should be in the public Internet, it needs to be secure, thus core connects to the proxy.

This way **core can be in an Intranet network segment and proxy can be in DMZ, making Core completely cut-off on firewall from the Internet** (you only can have only outgoing firewall rules from Intranet allowing only for core to connect to proxy).

So **High Availability for core and proxy** gets complicated, with multiple proxies core needs to manage those connections. We already have most of the code for that ready, but it's not yet production ready.

#### How to bullet-proof proxy & core with failover?

We recommend to deploy them on a failover solution - like on a kubernetes cluster (even small one - like mini-kube) . This way, kubernetes manages: healthchecks and does failover. You can have cluster N-nodes and if any VM/node with Core/Proxy goes offline or health checks fail - it's migrated to a new node.
