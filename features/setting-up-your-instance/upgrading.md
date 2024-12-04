---
icon: chevrons-up
description: Notes on upgrading Defguard and its components
---

# Upgrading

{% hint style="warning" %}
Before doing any updates please remember to **backup your database.**
{% endhint %}

## Any previous core release -> core 1.1.4

### Core

{% hint style="danger" %}
In Core 1.1.4, we've made email addresses case insensitive, as this is a standard for many major providers. Because the emails were case sensitive up to this point, you may end up with users with the same email addresses from core's point of view. &#x20;
{% endhint %}

All email addresses must be unique case-insensitively, meaning that a user with an address `address@email.com` can't coexist with another user with an address `ADDRESS@email.com`. Before upgrading, make sure you don't have any users with the same email addresses given the above. If you do, please change those addresses or remove the users altogether. Remember to check it case-insensitively. If you have users with duplicate email addresses, the migrations will fail and you won't be able to upgrade.

You can use the following SQL query to locate users with duplicate emails in the database:

```sql
select id, username, email from "user" where lower(email) in (
	select lower(email) from "user" group by lower(email) having count(*) > 1
)
```

## 1.0.0 -> 1.1.0

### Proxy

There is a new setting:

* ENV Variable: DEFGUARD\_PROXY\_URL
* command line argument `--url`
* /etc/defguard/proxy.toml: `url =`&#x20;

**Which should be set to the same value as in core `DEFGUARD_ENROLLMENT_URL`**

## Any release -> 1.0.0

### Core

When upgrading core to 1.0.0 (even to a 1.0.0 pre-release) make sure that your users **have unique email addresses** as we've introduced a constraint requiring email addresses to be unique among users.&#x20;

{% hint style="danger" %}
If you have duplicate emails in your database, the migrations during the upgrade process will simply fail.
{% endhint %}

&#x20;You will need to change a duplicate email address before the upgrade by hand via the Defguard dashboard or by accessing the database.

### Desktop Client Real Time Sync

From 1.0.0 we have introduced [Enterprise features](../../enterprise/all-enteprise-features/),  and one of them is [automatic and real-time desktop client configuration synchronisation](../../enterprise/all-enteprise-features/automatic-real-time-desktop-client-configuration.md).

To enable this on an **already configured desktop client** one must perform one time instance update, which will generate necessary tokens on the client to perform from now on automatic updates. In details:

1. The admin must generate a new token for the client -[ more details here](../../admin-and-features/features-and-configuration/wireguard/remote-desktop-activation.md) (token can be sent over email or shared in any other secret way).
2. The user must perform the [Instance Update - more details here](../../help/configuring-vpn/add-new-instance/update-instance.md#how-to-update).

{% hint style="warning" %}
Any client that is configured from scratch has this done automatically and no actions needed to be done.
{% endhint %}

## Core 0.8.x -> 0.9.x with Proxy 0.2.x -> 0.3.x

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

## Desktop Client 0.1.x -> 0.2.0

With this release we have added Multi-Factor Authentication  to the desktop client. Unfortunately desktop client database has change significantly as well as business logic (for example endpoints to proxy for MFA handshake). We have not stored them previously in the database - thus they cannot be recovered/updated automatically.

{% hint style="warning" %}
That unfortunately means you have to remove all your instances before upgrading (or just remove any desktop client configuration files, including the database) and start the enrollment (adding new instance) again after upgrading - just by adding a new device (you can remove the old one).
{% endhint %}
