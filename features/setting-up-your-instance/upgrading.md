---
description: Notes on upgrading Defguard and its components
---

# Upgrading

{% hint style="warning" %}
Before doing any updates please remember to **backup your database.**
{% endhint %}

## Core & Proxy

### Core 0.8.x -> 0.9.x with Proxy 0.2.x -> 0.3.x

In this release, we have **hardened the security architecture**, and since the Proxy component is open for HTTP commands and is frequently communicating with Core we have reversed the communication and now **Core is connecting to Proxy (Proxy is a gRPC server and Core is the client).**

This way if Core is in a secure network segment (like Intranet) and Proxy in a DMZ segment (where Internet traffic is allowed) you don't need to open on your firewall rules for Proxy from DMZ to connect to Intranet (no packet for New Connections from DMZ->Intranet).

This change requires a few changes if you are upgrading:

#### Proxy deployment configuration

1. Remove `DEFGUARD_PROXY_UPSTREAM_GRPC_URL` variable - since Proxy does not connect to  defguard Core any more.
2. Proxy is now the server to which defguard Core connects, so you may want to:
   1. Optional: configure non-default Proxy gRPC port with `DEFGUARD_PROXY_GRPC_PORT -` default value is **50051**&#x20;
   2. If you have a Proxy in a different network segment - eg. have a custom installation (not with one-line install/docker compose all on one server) - you may also consider exposing the gRPC port and reverse-proxy (nginx/treafik/...) the port with SSL/TLS.
      1. (Optional) If you want to use SSL with Proxy gRPC server without revers-proxy (nginx/etc) configure  `DEFGUARD_PROXY_GRPC_CERT` and `DEFGUARD_PROXY_GRPC_KEY` following the [SSL setup guide](docker-compose.md#grpc-ssl-setup).
   3. Also adjust your firewall config to open new Docker port mapping etc. Make sure Proxy gRPC server **can be reached from Core**.

#### Core deployment configuration

1. Add `DEFGUARD_PROXY_URL` variable to point to your Proxy gRPC server endpoint, for example `http://proxy:50051` when using Docker Compose - or any gRPC URL you have configured with your reverse proxy.
2. (Optional) If using SSL configure `DEFGUARD_PROXY_GRPC_CA`

#### Upgrade process

1. Update Core & Proxy images/binaries and restart services.
2. You should see in the logs that Proxy is awaiting a gRPC connection - example docker logs:

```
Attaching to defguard_proxy_1
proxy_1  | 2024-01-24T14:05:41.365035Z  INFO defguard_proxy::server: Starting Defguard proxy server
proxy_1  | 2024-01-24T14:05:41.365069Z DEBUG defguard_proxy::server: Setting up API server
proxy_1  | 2024-01-24T14:05:41.365130Z  INFO defguard_proxy::server: gRPC server is listening on 0.0.0.0:50051
proxy_1  | 2024-01-24T14:05:41.365333Z  INFO defguard_proxy::server: Web server is listening on 0.0.0.0:8080
```

3. Core should be attempting to establish a gRPC connection with Proxy (and retrying every 10s if unable to successfully connect), like this:

```
defguard | 2024-01-24T14:17:47.815294Z  INFO defguard::grpc: Connecting to proxy
```

4. After defguard connects successfully to proxy, you should see in proxy logs:

```
proxy_1  | 2024-01-24T14:17:47.819504Z  INFO defguard_proxy: RPC client connected from: 10.123.123.2:35916
```

