# Create/manage VPN Location

A VPN location is a VPN network to which users can connect to. Every location has a [dedicated gateway](../../setting-up-your-instance/gateway/) (or [multiple gateways if you deploy a high-availability solution](../../setting-up-your-instance/high-availability-and-failover.md#gateway-high-availability)).

{% hint style="success" %}
Defguard supports **multiple locations**, for each location to work you need to configure it and deploy a dedicated gateway.
{% endhint %}

{% hint style="info" %}
If you are looking for MFA settings, go here.
{% endhint %}

When creating a new VPN location you can choose if you want to **create it from scratch (Manual Configuration)** or **import your current WireGuard configuration**:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-21 at 14.19.04.png" alt=""><figcaption></figcaption></figure>

## VPN Location settings

Next step is configuring the location settings:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-21 at 14.29.53.png" alt=""><figcaption></figcaption></figure>

### Location name

It's a name that will be visible both on the UI, but also in the desktop client for all the users. For example if you name your location _Monaco Office_, the desktop client will show:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-21 at 14.37.51.png" alt="" width="375"><figcaption></figcaption></figure>

### Gateway VPN IP addresses and masks

By providing the VPN IPs/masks, you are configuring both: **the VPN internal networks and VPN server IPs**. Every gateway will bind to these addresses and Defguard will also generate and assign IP addresses for devices in this location from these networks.&#x20;

This field can contain multiple IP addresses (both IPv4 and IPv6), separated by a comma (e.g. `10.10.20.1/24,fc00::abcd:0:1/96`).

{% hint style="info" %}
## Dual-stack VPN networks

Defguard supports dual-stack VPN networks, allowing simultaneous assignment of both IPv4 and IPv6 addresses to clients. Each VPN network can include multiple IPv4 and IPv6 subnets, and connected clients will automatically receive one address from each defined subnet. This enables seamless communication over both IP versions within a single VPN session.
{% endhint %}

{% hint style="warning" %}
Defguard assigns IP addresses to clients by sequentially scanning each defined subnet and selecting the first available address. If no free address is found in any of the configured networks, the client will not receive an IP assignment. In such cases, you’ll need to adjust the network configuration - such as expanding the address pool by decreasing the netmask - to accommodate additional clients.
{% endhint %}

#### Examples

1. 10.11.0.1/8
   1. internal VPN network will be: 10.11.0.0 with netmask 255.0.0.0
   2. VPN gateway internal IP address will be: 10.11.0.1
2. 192.168.8.1/24,fc00::1/112
   1. internal VPN networks will be: `192.168.8.0` with netmask `255.255.255.0` and `fc00::0` with netmask FFFF:FFFF:FFFF:FFFF:FFFF:FFFF:FFFF:0000
   2. VPN gateway internal IP addresses will be: 192.168.8.1 and fc00::1

### Gateway address

It's the **public IP** address to which the remote peer's/users will connect to. This IP address is **will be shared in the configuration** for the clients, but defguard gateways do **not bind to this address**.

{% hint style="warning" %}
Defguard gateways bind to all IP addresses and the port defined below.

This is very handy if you are setting up a **high availability active-active** solution with multiple gateways - then this public IP needs to be exposed and controled by load-balancers or any other solution that will forward this to gateways.
{% endhint %}

### Gateway port

Defguard **gateways bind to this port** and this port is shared in configuration to any client.

### Allowed IPs

Defines the IP ranges a device is allowed to route or communicate with.

It supports multiple networks separated with comma, eg. 10.11.1.0/0, 192.168.1.0/24

{% hint style="danger" %}
Right now defguard only manages routing of AllowedIPs (adding to routing table the networks defined in AllowedIPs).

If you want the _All Traffic_ to work in the desktop client you need to also configure MASQUARED/NAT for the VPN interface. [Example of that here.](../../../tutorials/step-by-step-setting-up-a-vpn-server/#enabling-to-access-internet-through-your-vpn)
{% endhint %}

### DNS

This specifies DNS resolvers and search domains. Supported format is by comma separation, eg.:

`IP, IP, search.domain.net, second.search.domain.com`

### Allowed groups

Here you can specify **what groups (users assigned to those groups) have access to this VPN Location.**

{% hint style="warning" %}
By default (if no group is chosen) **all users will have access to this location.**

By defining a group, assigning users to that group and then choosing this group(s) you can restrict access to VPN Locations.
{% endhint %}

### Multi-Factor Authentication for a Location

#### Require MFA for this location

By enabling this setting this location **will require Multi-Factor Authentication** on each connection to this location.

{% hint style="danger" %}
This feature is only supported in [**Defguard Desktop Client**](../../../help/desktop-client.md)**.**
{% endhint %}

Each connection in the client:

1. Will require the user to provide either TOTP token or Email code.
2. After authorizing defguard will do a key exchange and setup a pre-shared session key unique for this connection.

{% hint style="warning" %}
For this feature to work, the user must:

1. configure their [TOTP settings in the profile](../../../help/setting-up-2fa-mfa.md#one-time-password)
2. [SMTP settings needs to be set up](../setting-up-smtp-for-email-notifications.md) and the user must enable Email tokens in their profile.
{% endhint %}

#### Keepalive interval

Configurable time interval (in seconds) used to send periodic packets to ensure that the connection remains active. This is particularly useful in environments like NAT (Network Address Translation) or firewalls that may close idle connections.

**Peer disconnect threshold**

Since Multi-Factor Authentication (MFA) is used to enforce zero-trust security, a peer (user) that remains inactive for a specified time interval (defined in seconds within the settings) will be disconnected. Additionally, the session configuration will be removed from the gateway. This ensures that when the peer reconnects, they must complete the MFA process again.

{% hint style="warning" %}
Minimal value for this setting is 120 (2 minutes).

Recommended is more then 300.
{% endhint %}
