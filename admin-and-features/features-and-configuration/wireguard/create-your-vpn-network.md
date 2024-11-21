# Create new VPN Location

A VPN location is a VPN network to which users can connect to. Every location has a [dedicated gateway](../../setting-up-your-instance/gateway/) (or [multiple gateways if you deploy a high-availability solution](../../setting-up-your-instance/high-availability-and-failover.md#gateway-high-availability)).

{% hint style="success" %}
Defguard supports **multiple locations**, for each location to work you need to configure it and deploy a dedicated gateway.
{% endhint %}

When creating a new VPN location you can choose if you want to **create it from scratch (Manual Configuration)** or **import your current WireGuard configuration**:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-21 at 14.19.04.png" alt=""><figcaption></figcaption></figure>

## VPN Location settings

Next step is configuring the location settings:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-21 at 14.29.53.png" alt=""><figcaption></figcaption></figure>

### Location name

It's a name that will be visible both on the UI, but also in the desktop client for all the users. For exaple if you name your location _Monaco Office_, the desktop client will show:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-21 at 14.37.51.png" alt="" width="375"><figcaption></figcaption></figure>

### **Gateway VPN IP address and mask:**

By providing the VPN IP/mask, you are configuring both: **the VPN internal network and VPN server IP**. Every gateway will bind to this address and defguard will also generate and assign IP addresses for devices in this location from this network.

#### Examples

1. 10.11.0.1/8
   1. internal VPN network will be: 10.11.0.0 with netmask 255.0.0.0
   2. VPN gateway internal ip will be: 10.11.0.1
2. 192.168.8.1/24
   1. internal VPN network will be: 192.168.8.0 with netmask 255.255.255.0
   2. VPN gateway internal ip will be: 192.168.8.1

### Gateway address

It's the **public IP** address to which the remote peer's/users will connect to. This IP address is **will be shared in the configuration** for the clients, but defguard gateways do **not bind to this address**.&#x20;

{% hint style="warning" %}
Defguard gateways bind to all IP addresses and the port defined below.

This is very handy if you are setting up a **high availability active-active** solution with multiple gateways - then this public IP needs to be exposed and controled by load-balancers or any other solution that will forward this to gateways.
{% endhint %}

### Gateway port

Defguard **gateways bind to this port** and this port is shared in configuration to any client.

### Allowed IPs

Defines the IP ranges a device is allowed to route or communicate with.

It supports multiple networks separated with comma, eg. 10.11.1.0/0, 192.168.1.0/24

### DNS

DNS specifies DNS resolver to query when the WireGuard interface is up.

{% hint style="danger" %}
For now defguard (and defguard client) **only supports a single DNS server** (single IP) and doesn't support DNS search domains.

So dns server should be one IP, like: 10.10.10.1
{% endhint %}
