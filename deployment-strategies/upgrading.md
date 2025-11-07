---
description: Notes on upgrading Defguard and its components
---

# Migration guides

{% hint style="warning" %}
Before doing any updates please remember to **backup your database.**
{% endhint %}

## 1.5.x -> 1.6.0

### Core

#### Service locations

A new feature has been introduced: [service-locations.md](../features/service-locations.md "mention"). This feature requires both Defguard Core and Proxy to be updated to the 1.6.0 version. Updating only one of those components will prevent this feature from working properly.

### Proxy

#### Service locations

As mentioned in the Core migration section, this feature requires both Defguard Core and Proxy to be updated to the 1.6.0 version.

### Desktop client

#### Service locations

The [service-locations.md](../features/service-locations.md "mention") feature currently only works on the Windows Desktop Client. Locations in the service location mode won't be sent to clients that don't support them (either are older than 1.6.0 or are on a different platform than Windows).

To work properly, this feature requires the Desktop Client to be in version 1.6. Service locations won't show up or work in older clients.

#### Windows client changes

The Windows Desktop Client installer has changed. The installer is now provided in an `.msi` format.

Installing the new 1.6.0 Client from the `.msi` will leave the previous Client version still installed. This can also cause old VPN connections to still be active until a next system restart is performed.

To resolve this, before upgrading, we recommend first uninstalling the old Client. This will leave your configuration intact and it should carry over to the new Client after its installation, without the need to configure everything again.

## 1.4.x -> 1.5.0

### Core

#### Version compatibility

This release introduces a version checking system. All the Defguard system components (core, proxy, gateway and clients) are now version-aware and check their compatibility with the components their are communicating with.

This might mean that until you upgrade all the components your web UI might indicate that your proxy or gateway is of an unknown version.

#### Instance UUID bug

A bug resulting in zeroing the UUID of a given instance has been found and resolved ([PR link](https://github.com/DefGuard/defguard/pull/1521)).&#x20;

This value is used by the desktop client to identify instances. The new client should gracefully handle migration to a new UUID if it has been zeroed out due to the bug mentioned above.

#### Verify client disconnect threshold for your MFA locations

In order to ensure that MFA works correctly with the new [mobile clients](../using-defguard-for-end-users/mobile-client/) please ensure that the [client disconnect threshold](../features/wireguard/create-your-vpn-network.md#client-disconnect-threshold) is set to at least 300s (5 minutes).

### Proxy

We've introduced a new functionality to Desktop Client - to authenticate [Multi-Factor connections using Mobile Client](../using-defguard-for-end-users/desktop-client/using-multi-factor-authentication-mfa.md). For this feature to work, Proxy (enrollment service) creates a Web-socket that the desktop client connects to while waiting for responses from the mobile client.

{% hint style="warning" %}
If you have a reverse proxy for the enrollment service (which we highly recommend with SSL termination), please **make sure that web-sockets are enabled.**
{% endhint %}

### Desktop client

#### Unix socket IPC and new user group requirement (macOS & Linux)

macOS and Linux clients now use Unix sockets for IPC. To securely access this socket the user must belong to a specific group as described in [client documentation](../using-defguard-for-end-users/desktop-client/).

The change should require no additional steps for macOS users, but Linux users who install the client from official packages will need to log out and back in or reboot after install to refresh group membership. This will not be required on subsequent updates.

Linux users who use release binaries will need to manually create the `defguard` group and adjust their group membership.

## Any release <= 1.3 -> 1.4

1.4 release introduces changes related to multiple client IP addresses. To ensure compatibility, **all components must be updated** to v1.4 or higher:

* **Core**
* **Proxy**
* **Gateway**
* **Desktop Clients**

Running outdated versions may result in errors due to incompatible data formats.

### Core

We've made a small update to the LDAP integration to support more complex user nesting within the LDAP tree ([related issue](https://github.com/DefGuard/defguard/issues/1242)).

If you were already using the integration, you shouldn't notice any changes. However, we **strongly recommend backing up your database before the upgrade and afterwards verifying** the following to ensure everything continues to work as expected:

* Your Defguard user list and user devices remain unchanged
* All users can still log in without issues

If you encounter any problems, please report them on our [GitHub](https://github.com/DefGuard/defguard/issues).

## Any previous release → 1.4.0-alpha3

We've introduced some changes to the LDAP integration. We recommend reading [the above section](upgrading.md#core) before upgrading.

## Any previous release → 1.3.0

* The LDAP integration has become an enterprise feature. You will need to purchase the enterprise license if you exceed the free limits. See [license.md](../enterprise/license.md "mention") for more information regarding the license.
*   If you used the LDAP integration previously, it will be off by default after upgrading. You will have to manually enable it in the settings in the LDAP tab:\\

    <figure><img src="../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

## Any previous 1.3.0 alpha → 1.3.0 alpha 4

### Core

LDAP integration received a major overhaul of how users are mapped to Defguard users when the two-way synchronization is enabled. Now, users are always identified by their leftmost DN value.

A new synchronization may cause some of your users to be re-added, which in turn may cause the loss of some of their Defguard specific data (e.g. their devices). This will happen if your leftmost DN component's attribute (referred to as RDN) is not the same as your current username attribute. This issue is only related to the two-way synchronization mechanism and occurs only if you used one of the previous alphas of 1.3.0. Upgrading from any previous release to alpha 4 (skipping the alphas before) should not result in this happening.

Before an upgrade, turn off the two-way synchronization. After upgrading, you will have access to a new option, the RDN user attribute:

<figure><img src="../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

Set it according to your LDAP server setup. This should be the DN's leftmost component attribute, e.g. in the case of `cn=user1,cn=users,dc=ad,dc=example,dc=com` this would be "cn". This attribute is needed to properly identify users in your LDAP server. The username attribute will be mapped to Defguard usernames. Read [settings-table.md](../features/ldap-and-active-directory-integration/settings-table.md "mention") for a description of those settings options. After you configured this value, you can re-enable the two-way synchronization.

## Any previous core release -> core 1.1.4

### Core

{% hint style="danger" %}
In Core 1.1.4, we've made email addresses case insensitive, as this is a standard for many major providers. Because the emails were case sensitive up to this point, you may end up with users with the same email addresses from core's point of view.
{% endhint %}

All email addresses must be unique case-insensitively, meaning that a user with an address `address@email.com` can't coexist with another user with an address `ADDRESS@email.com`. Before upgrading, make sure you don't have any users with the same email addresses given the above. If you do, please change those addresses or remove the users altogether. Remember to check it case-insensitively. If you have users with duplicate email addresses, the migrations will fail, and you won't be able to upgrade.

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
* /etc/defguard/proxy.toml: `url =`

**Which should be set to the same value as in core `DEFGUARD_ENROLLMENT_URL`**

## Any release -> 1.0.0

### Core

When upgrading core to 1.0.0 (even to a 1.0.0 pre-release) make sure that your users **have unique email addresses** as we've introduced a constraint requiring email addresses to be unique among users.

{% hint style="danger" %}
If you have duplicate emails in your database, the migrations during the upgrade process will simply fail.
{% endhint %}

You will need to change a duplicate email address before the upgrade by hand via the Defguard dashboard or by accessing the database.

### Desktop Client Real Time Sync

From 1.0.0 we have introduced [Enterprise features](https://github.com/DefGuard/docs/blob/docs/deployment-strategies/broken-reference/README.md), and one of them is [automatic and real-time desktop client configuration synchronization](../features/remote-user-enrollment/automatic-real-time-desktop-client-configuration.md).

To enable this on an **already configured desktop client,** one must perform one time instance update, which will generate necessary tokens on the client to perform from now on automatic updates. In details:

1. The admin must generate a new token for the client -[ more details here](../features/wireguard/remote-desktop-activation.md) (token can be sent over email or shared in any other secret way).
2. The user must perform the [Instance Update - more details here](../using-defguard-for-end-users/desktop-client/instance-configuration.md#updating-instance).

{% hint style="warning" %}
Any client that is configured from scratch has this done automatically and no actions needed to be done.
{% endhint %}

## Core 0.8.x -> 0.9.x with Proxy 0.2.x -> 0.3.x

In this release, we have **hardened the security architecture**, and since the Proxy component is open for HTTP commands and is frequently communicating with Core we have reversed the communication and now **Core is connecting to Proxy (Proxy is a gRPC server and Core is the client).**

This way if Core is in a secure network segment (like Intranet) and Proxy in a DMZ segment (where Internet traffic is allowed) you don't need to open on your firewall rules for Proxy from DMZ to connect to Intranet (no packet for New Connections from DMZ->Intranet).

This change requires a few changes if you are upgrading:

#### Proxy deployment configuration

1. Remove `DEFGUARD_PROXY_UPSTREAM_GRPC_URL` variable - since Proxy does not connect to Defguard Core any more.
2. Proxy is now the server to which Defguard Core connects, so you may want to:
   1. Optional: configure non-default Proxy gRPC port with `DEFGUARD_PROXY_GRPC_PORT -` default value is **50051**
   2. If you have a Proxy in a different network segment - eg. have a custom installation (not with one-line install/docker compose all on one server) - you may also consider exposing the gRPC port and reverse-proxy (nginx/treafik/...) the port with SSL/TLS.
      1. (Optional) If you want to use SSL with Proxy gRPC server without revers-proxy (nginx/etc) configure `DEFGUARD_PROXY_GRPC_CERT` and `DEFGUARD_PROXY_GRPC_KEY` following the [SSL setup guide](docker-compose.md#grpc-ssl-setup).
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

4. After Defguard connects successfully to proxy, you should see in proxy logs:

```
proxy_1  | 2024-01-24T14:17:47.819504Z  INFO defguard_proxy: RPC client connected from: 10.123.123.2:35916
```

## Desktop Client 0.1.x -> 0.2.0

With this release we have added Multi-Factor Authentication to the desktop client. Unfortunately desktop client database has change significantly as well as business logic (for example endpoints to proxy for MFA handshake). We have not stored them previously in the database - thus they cannot be recovered/updated automatically.

{% hint style="warning" %}
That unfortunately means you have to remove all your instances before upgrading (or just remove any desktop client configuration files, including the database) and start the enrollment (adding new instance) again after upgrading - just by adding a new device (you can remove the old one).
{% endhint %}
