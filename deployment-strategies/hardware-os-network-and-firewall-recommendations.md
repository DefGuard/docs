---
description: >-
  Before Defguard can be deployed please get familiar with the following
  recommendations
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/hardware-os-network-and-firewall-recommendations
---

# Hardware, OS, network and firewall recommendations

## Server & environment requirements

Defguard can be deployed on multiple servers (physical or virtual) or on a single server (which is not recommended).

Recommended setup reflects the [general system architecture](../in-depth/architecture/) with components being split into three separate machines:

1. **Dedicated server or Virtual Machine for Core (control plane)** - that is in the Intranet network segment, not exposed in the public Internet in any way. Core needs to be accessible from the local (secure) network and VPN (to access Defguard securely). Recommended hardware parameters:
   1. CPU: min. 1 CPU/vCPU per location - eg. if Defguard handles 2 VPN locations recommended is min. 2 CPU/vCPU
   2. RAM: min. 1GB per location
   3. Disk: min 8GB and more (since statistics will be gathered)
2. **Dedicated server or Virtual Machine for Edge (external and public enrollment service)** - this server/VM needs to be deployed in DMZ/public/external systems network segment - as this service will be exposed and must be available publicly from the Internet. Recommended hardware parameters:
   1. CPU: min. 1 CPU/vCPU per location
   2. RAM: min. 1GB
   3. Disk: min 1GB
3. **Dedicated server or Virtual Machine for Gateway -** this server/VM needs to be deployed in:
   1. DMZ/public/external systems network segment - as this service will be exposed and must be available publicly from the Internet.
   2. Has access on Internal network interfaces to all network segments that will be exposed from VPN for users.
   3. Recommended hardware parameters:
      1. CPU: min. 1 CPU/vCPU per location
      2. RAM: min. 1GB
      3. Disk: min 4GB (mostly for logs)

In general the hardware requirements will also have to be adjusted based on the number of active users. The numbers above should serve as a baseline.

### Operating system and software requirements

#### Package based installation

Package based install requires Debian GNU/Linux min. 13.x or Ubuntu Linux min. 24.04.x

#### Docker based installation

Docker deployment requires the system to have [official Docker Engine installation](https://docs.docker.com/engine/install/) (not distribution based packages).

## Network IP & DNS setup

### Gateway server - where WireGuard VPN tunnels itself will be launched

* **The** [**Gateway address**](../features/wireguard/create-your-vpn-network.md#gateway-address) and[ **Gateway Port**](../features/wireguard/create-your-vpn-network.md#gateway-port) **must be publicly available from the Internet**

{% hint style="warning" %}
The server on which the Gateway is installed does not need to have the IP address (the same as the Gateway Address) assigned to it - can have internal network address.

The Gateway Address is the address specified in the clients’ configuration – therefore, if this address is assigned for example to a Firewall or Load Balancer rather than the server hosting the Gateway, **the port from this address (Gateway Port) must be forwarded (e.g., via NAT) to the Gateway Port on the server where the Gateway is installed.**
{% endhint %}

* must have all networks on internal interfaces addresses configured, that should be accessible from VPN
* **Recommended:** to have a public domain assigned to this IP for VPN server, eg. _vpn.company.com_

### Edge - public web service for enrollment & desktop client configuration

{% hint style="warning" %}
The server on which the Proxy is installed does not need to have the IP address assigned to it which the enrollment URL domain points to - can have internal network address.

If this address is assigned for example to a Firewall or Load Balancer rather than the server hosting the Gateway, **the port from this address (eg. if the enrollment URL is https://vpn-config.domain.com, then the port is 443) must be forwarded (e.g., via NAT) to the** [**DEFGUARD\_PROXY\_HTTP\_PORT**](https://docs.defguard.net/deployment-strategies/configuration#proxy-service) **on the server where the Proxy is installed.**
{% endhint %}

* **must have a public enrollment domain assigned to this IP,&#x20;**_**eg. enrollment.company.com (or vpn-config.company.com, etc..**_**)**

### Core & database server

* should be internal / private IP addresses accessible only from Intranet and VPN
* must have internal domain name assigned in the local network DNS server, eg. _defguard.company.com_

## Firewall settings

### Hardened and most secure architecture

Below is a typical Enterprise network segmentation diagram showing the minimum required segments for a De-Militarized Zone (DMZ) and the Intranet, along with the recommended placement of Defguard components within this setup:

<figure><img src="../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

### One firewall setup

For organizations with simpler network setups, we recommend an architecture that isolates Defguard components using VLANs:

<figure><img src="../.gitbook/assets/single-firewall.png" alt=""><figcaption></figcaption></figure>

### Port & Firewall exposure summary

#### Gateway

1. Please open the private **internal** **TCP port 50066** to which to which the Core can connect to and adopt and manage the Gateway automatically.
2. Please open the **public port you wish the WireGuard® VPN to be working on - eg. 50051** (default for new location) or **51820** (default for all-in-one Docker/OVA setup).

#### Edge

If you configured your own Reverse Proxy for Edge then expose the reverse proxy with your preference.

If you have used Defguard's internal SSL termination please expose on the machine (or forward to Edge):

1. Open the **public TCP 443 port** on the server (**https**).
   1. If you are using Defguard's automatic Let's Encrypt SSL certificate configuration please also open port TCP 80 (http) - as Let's Encrypt requires this port for validating the domain and obtaining the certificate.



Please open an **internal TCP 50051 port** to which to which the Core can connect to and adopt and manage the Edge automatically.

#### Core

If you configured your own Reverse Proxy for Core then expose the reverse proxy in your internal network with your preference.

If you have used Defguard's internal SSL termination please expose on the machine (or forward to Core):

1. TCP 443 (https) port for web interface accessible only from local/VPN network.

{% hint style="warning" %}
**Please make sure that Core can connect to Edge and Gateway internal ports mentioned above.**
{% endhint %}

## Backup strategy

In a production environment you should use your preferred backup solution to secure the following:

* service configuration (.env file, service config files, compose configuration)
* database content (prefferably by doing a regular pgdump, not just filesystem-level backup)
