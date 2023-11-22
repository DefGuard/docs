---
description: This is a high-level project roadmap of planned features.
---

# Roadmap

A detailed [Roadmap with **all functionalities**  is on GitHub](https://github.com/orgs/DefGuard/projects/5/views/1) - here is a high level roadmap with major features planed.

For already implemented features go to [Changelog](../in-depth/changelog.md) (or [GitHub release page](https://github.com/DefGuard/defguard/releases) for more details).

## v0.8.0 - Desktop clients \~end of November 2023

### Problem: desktop clients (linux/mac/windows)

Right now we require the user to download the configuration and configure their VPN client manually. That needs to be automatic without user interaction/configuration.

### Solution

Our [gateway](https://github.com/DefGuard/gateway) is already a Wireguard _client_ that automatically based on GRPC configuration from our _Core Server_ configures the gateway to accept user/client connections/peers.

We need to implement a [Tauri](https://tauri.app/)-based desktop app (for all platforms: win/mac/linux) that will:

1. Allow the user to authenticate with their user/pass and Multi-factor method
2. Automatically add/configure a device
3. Provide a list of locations to which the client can connect
4. Display statistics of the connection

## v0.9.0 - groups & ACLs, Site-to-Site Wireguard VPN -  \~Jan 2024

### Problem

Currently, defguard has only two groups: All users and admins that have limited Access Control:

* admins can manage defguard/users
* from [v0.6.0](roadmap.md#v0.6.0-multiple-vpn-locations-planned-end-of-july-2023) a VPN Location can be configured to allow access restriction based on this two groups

### Solution

We need to implement a proper group and ACL management functionality, that will allow:

* fine-grained control over VPN/Location network management&#x20;
* fine-grained control of defguard functionalities based on ACLs
* add a Groups claim to OpenID tokens for any combination of groups/ACLs

### Problem

Currently, defguard has MFA configuration per user, there is no way to **globally** define by admin if MFA is required for users.

### Solution

Enable global MFA requirement.

### Other features planned

* Passkey support
* Command line client that will have functionalities:
  * Import & creat users in bulk from JSON/CSV
  * Show VPN status for all locations in human-readable way (person-device and not public keys like Wireguard/wg does)
* Password reset

## v1.0.0 - Wireguard mesh, on-demand NAT traversal, SAML SSO \~Feb 2024

### Problem: on-demand NAT traversal/mesh networks

Currently deploying defguard requires to a) have a public IP address and b) open a Wireguard VPN port on your firewall server (for our gateway microservice).

A lot of people/companies do not have the luxury of a Public IP or do not want to open **any ports** on their firewalls/routers.

The most popular service implementing this type of solution is [Talescale](https://tailscale.com/) - the goal of this milestone is to implement an open-source Tailescale and provide relays to ensure client connectivity in adverse network conditions, such as networks with blocked UDP, NAT, etc.

### Solution

We already have a PoC (_proof of concept)_ of secure peer-to-peer communication without the central gateway. In order to provide this functionality we need first to implement our[ own desktop clients](roadmap.md#problem-desktop-clients-linux-mac-windows) in order to incorporate this feature.

### Other features planned

* **SAML SSO** - most _Old School_ enterprise systems implement SSO based on SAML v2 - so to provide our users with a full range of features this should be implemented.
* **Prometheus exporter** - all activities handled by defguard will be exported to Prometheus so that you can create custom analytics and dashboards in your favorite tool from the Prometheus ecosystem
* Secure **SSH authentication based on OpenID Connect & Multi-Factor Authentication** with Defguard

## Further functionalities planned

* 2FA/MFA functionality with **phone/SMS codes**
* **Password reset secured by MFA** (SMS/TOTP/Webauthn/...)
* **Mobile clients**
* **Simple DNS provider**





