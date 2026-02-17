---
description: >-
  Before Defguard can be deployed please get familiar with the following
  recommendations
---

# Hardware, OS, network and firewall recommendations

## Server & environment requirements

Defguard can be deployed on multiple servers (physical or virtual) or on a single server (which is not recommended).

Recommended setup reflects the [general system architecture](../in-depth/architecture/) with components being split into three separate machines:

1. **Dedicated server or Virtual Machine for Core (control plane)** - that is in the Intranet network segment, not exposed in the public Internet in any way. Core needs to be accessible from the local (secure) network and VPN (to access Defguard securely). Recommended hardware parameters:
   1. CPU: min. 1 CPU/vCPU per location - eg. if Defguard handles 2 VPN locations recommended is min. 2 CPU/vCPU
   2. RAM: min. 1GB per location
   3. Disk: min 8GB and more (since statistics will be gathered)
2. **Dedicated server or Virtual Machine for Proxy (external and public enrollment service)** - this server/VM needs to be deployed in DMZ/public/external systems network segment - as this service will be exposed and must be available publicly from the Internet. Recommended hardware parameters:
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

* **The** [**Gateway address**](../features/wireguard/create-your-vpn-network.md) and[ **Gateway Port**](../features/wireguard/create-your-vpn-network.md) **must be publicly available from the Internet**

{% hint style="warning" %}
The server on which the Gateway is installed does not need to have the IP address (the same as the Gateway Address) assigned to it - can have internal network address.

The Gateway Address is the address specified in the clients’ configuration – therefore, if this address is assigned for example to a Firewall or Load Balancer rather than the server hosting the Gateway, **the port from this address (Gateway Port) must be forwarded (e.g., via NAT) to the Gateway Port on the server where the Gateway is installed.**
{% endhint %}

* must have all networks on internal interfaces addresses configured, that should be accessible from VPN
* **Recommended:** to have a public domain assigned to this IP for VPN server, eg. _vpn.company.com_

### Proxy - public web service for enrollment & desktop client configuration

* **The** [**enrollment URL**](https://docs.defguard.net/deployment-strategies/configuration#enrollment-configuration) **(that proxy will be configured under and available for user and clients to reach) needs to be publicly available from the Internet.**

{% hint style="warning" %}
The server on which the Proxy is installed does not need to have the IP address assigned to it which the enrollment URL domain points to - can have internal network address.

If this address is assigned for example to a Firewall or Load Balancer rather than the server hosting the Gateway, **the port from this address (eg. if the enrollment URL is https://vpn-config.domain.com, then the port is 443) must be forwarded (e.g., via NAT) to the** [**DEFGUARD\_PROXY\_HTTP\_PORT**](https://docs.defguard.net/deployment-strategies/configuration#proxy-service) **on the server where the Proxy is installed.**
{% endhint %}

* **must have a public enrollment domain assigned to this IP,&#x20;**_**eg. enrollment.company.com (or vpn-config.company.com, etc..**_**)**

### Core & database server

* should be internal / private IP addresses accessible only from Intranet and VPN
* must have internal domain name assigned in the local network DNS server, eg. _defguard.company.com_

## Firewall settings

### Gateway

1. Please open the public port you wish the VPN to be working on - eg. 50555

### Proxy

1. Please open the public 443 port on the server (recommended to rewrite port 80 to redirect to 443)
2. Please open gRPC port on the internal network - so that the **Defguard Core can connect to this port - more details here:** [**https://docs.defguard.net/deployment-strategies/configuration#proxy-service**](https://docs.defguard.net/deployment-strategies/configuration#proxy-service)

### Core

1. Please open 443 port for web interface accessible only from local/VPN network
2. Please open a gRPC port **for the Gateway server to connect to this port via a local network - more info here:** [**https://docs.defguard.net/deployment-strategies/configuration#grpc-server-configuration**](https://docs.defguard.net/deployment-strategies/configuration#grpc-server-configuration)

## Backup strategy

In a production environment you should use your preferred backup solution to secure the following:

* service configuration (.env file, service config files, compose configuration)
* database content (prefferably by doing a regular pgdump, not just filesystem-level backup)
