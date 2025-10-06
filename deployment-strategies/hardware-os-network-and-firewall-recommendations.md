---
description: >-
  Before Defguard can be deployed please get familiar with the following
  recommendations
---

# Hardware, OS, network and firewall recommendations

## Server & environment requirements

Defguard can be deployed on multiple servers (physical or virtual) or on a single server (which is not recommended).

Recommended setup:

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

### Operating system and software requirements

#### Package based installation

Package based install requires Debian GNU/Linux min. 13.x or Ubuntu Linux min. 24.04.x

#### Docker based installation

Docker deployment requires the system to have [official Docker Engine installation](https://docs.docker.com/engine/install/) (not distribution based packages).

## Network IP & DNS setup

### Gateway server - where WireGuard VPN tunnels itself will be launched

* **must have a public IP assigned on which the WireGuard port will be exposed in the Internet**
* must have all networks on internal interfaces addresses configured, that should be accessible from VPN
* **Recommended:** to have a public domain assigned to this IP for VPN server, eg. _vpn.company.com_

### Proxy - public web service for enrollment & desktop client configuration

* **must have a public IP assigned on which the enrollment domain will be configured and HTTPS server will be exposed**
* **must have a public enrollment domain assigned to this IP,&#x20;**_**eg. enrollment.company.com (or vpn-config.company.com, etc..**_**)**

### Core & database server

* should be internal / private IP addresses accessible only from Intranet and VPN
* must have internal domain name assigned in the local network DNS server, eg. _defguard.company.com_

## Firewall settings

### Gateway

1. Please open the public port you wish the VPN to be working on - eg. 50555

* Please open on the firewall: local network access **from the Gateway server/VM** → **to Defguard Core gRPC port - more info here:** [**https://docs.defguard.net/deployment-strategies/configuration#grpc-server-configuration**](https://docs.defguard.net/deployment-strategies/configuration#grpc-server-configuration)

### Proxy

1. please open the public 443 port on the server (recommended to rewrite port 80 to redirect to 443)
2. please open gRPC port on the internal network - so that the **Defguard Core can connect to this port - more details here:** [**https://docs.defguard.net/1.5/deployment-strategies/configuration#proxy-service**](https://docs.defguard.net/1.5/deployment-strategies/configuration#proxy-service)

### Core

1. please open 443 port for web interface accessible only from local/VPN network
2. please open a gRPC port **for the gateway server to connect to this port - more info here:** [**https://docs.defguard.net/deployment-strategies/configuration#grpc-server-configuration**](https://docs.defguard.net/deployment-strategies/configuration#grpc-server-configuration)
