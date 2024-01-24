---
description: Notes on upgrading Defguard and its components
---

# Upgrading

## Core & Proxy

### Core 0.8.x -> 0.9.x with Proxy 0.2.x -> 0.3.x

In this release we have **hardened the security architecture**, and since the Proxy component is open for HTTP commands and is frequently communicating with Core we have reversed the communication and now **Core is connecting to Proxy (Proxy is a gRPC server and Core is the client).**

This way if Core is in a secure network segment (like Intranet) and Proxy in a DMZ segment (where Internet traffic is allowed) you don't need to open on your firewall rules for Proxy from DMZ to connect to Intranet (no packet for New Connections from DMZ->Intranet).

This change requires few changes if you are upgrading:

1. Update environment variables/configuration options for Proxy:
   1. Remove `DEFGUARD_PROXY_UPSTREAM_GRPC_URL` variable
   2. (Optional) Configure non-default Proxy gRPC port with `DEFGUARD_PROXY_GRPC_PORT` (default value is 50051).
   3. Adjust your firewall config, Docker port mapping etc. if necessary to make sure Proxy gRPC server can be reached from Core.
   4. (Optional) If you want to use SSL with Proxy gRPC server configure  `DEFGUARD_PROXY_GRPC_CERT` and `DEFGUARD_PROXY_GRPC_KEY` following the [SSL setup guide](docker-compose.md#grpc-ssl-setup).
2. Update environment variables/configuration options for Core:
   1. Add `DEFGUARD_PROXY_URL` variable to point to your Proxy gRPC server endpoint, for example `http://proxy:50051` when using Docker Compose.
   2. (Optional) If using SSL configure `DEFGUARD_PROXY_GRPC_CA`
3. Update Core & Proxy images/binaries and restart services.
4. You should see in the logs that Proxy is awaiting a gRPC connection and Core is attempting to establish a gRPC connestion with Proxy (and retrying every 10s if unable to successfully connect)

## Desktop Client

### 0.1.x -> 0.2.0

With this release we have added Multi-Factor Authentication  to the desktop client. Unfortunately desktop client database has change significantly as well as business logic (for example endpoints to proxy for MFA handshake). We have not stored them previously in the database - thus they cannot be recovered/updated automatically.

{% hint style="warning" %}
That unfortunately means you have to remove all your instances before upgrading (or just remove any desktop client configuration files, including the database) and start the enrollment (adding new instance) again after upgrading - just by adding a new device (you can remove the old one).
{% endhint %}
