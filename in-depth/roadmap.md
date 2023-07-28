---
description: This is a high-level project roadmap of planned features.
---

# Roadmap

Find out the current milestone status on the [GitHub Milestone page](https://github.com/DefGuard/defguard/milestones).

## v0.7.0 - Remote user enrollment & on-boarding - end of Aug 2023

### Problem

defguard has a secure/microservice[ architecture](architecture.md) - and the best way to deploy defguard is to have the _core server_ inside the internal network (not exposed to the internet) or available for the user via VPN. This approach raises a significant problem in onboarding new remote users: _how can a user access defguard and add their devices to access VPN or change their password if they can't access defguard?_ Also, there is currently no mechanism to share for the user their login/password**.**&#x20;

### **Solution**

Implement a secure onboarding/enrollment service - [design for preview available on Figma](https://www.figma.com/file/uoFcgpOuVWa6g7tvKwB52o/defguard?type=design\&node-id=2138-5395\&mode=design\&t=fWyd8Cvl054jB4h4-0)

* that can be exposed to the Internet&#x20;
* available only from a secure link/with a one-time token - send **by email / printed**
* with functionalities:
  * **setup initial user password**
  * **add initial/first VPN device** to allow accessing internal/corporate network
  * **password reset** functionality

[Milestone implementation progress](https://github.com/defguard/defguard/milestones/2)

## v0.8.0 - on-demand NAT traversal/mesh & desktop clients - \~Sep 2023&#x20;

### Problem: desktop clients (linux/mac/windows)

Right now we require the user to download the configuration and configure their VPN client manually. That needs to be automatic without user interaction/configuration.

### Solution

Our [gateway](https://github.com/DefGuard/gateway) is already a Wireguard _client_ that automatically based on GRPC configuration from our _Core Server_ configures the gateway to accept user/client connections/peers.

We need to implement a [Tauri](https://tauri.app/)-based desktop app (for all platforms: win/mac/linux) that will:

1. Allow the user to authenticate with their user/pass and Multi-factor method
2. Automatically add/configure a device
3. Provide a list of locations to which the client can connect
4. Display statistics of the connection

### Problem: on-demand NAT traversal/mesh networks

Currently deploying defguard requires to a) have a public IP address and b) open a Wireguard VPN port on your firewall server (for our gateway microservice).

A lot of people/companies do not have the luxury of a Public IP or do not want to open **any ports** on their firewalls/routers.

The most popular service implementing this type of solution is [Talescale](https://tailscale.com/) - the goal of this milestone is to implement an open-source Tailescale and provide relays to ensure client connectivity in adverse network conditions, such as networks with blocked UDP, NAT, etc.

### Solution

We already have a PoC (_proof of concept)_ of secure peer-to-peer communication without the central gateway. In order to provide this functionality we need first to implement our[ own desktop clients](roadmap.md#problem-desktop-clients-linux-mac-windows) in order to incorporate this feature.

## v0.9.0 - groups & ACLs -  Oct/Nov 2023

### Problem

Currently, defguard has only two groups: All users & Admins that have limited Access Control:

* admins can manage defguard/users
* from [v0.6.0](roadmap.md#v0.6.0-multiple-vpn-locations-planned-end-of-july-2023) a VPN Location can be configured to allow access restriction based on this two groups

### Solution

We need to implement a proper group and ACL management functionality, that will allow:

* fine-grained control over VPN/Location network management&#x20;
* fine-grained control of defguard functionalities based on ACLs
* add a Groups claim to OpenID tokens for any combination of groups/ACLs

## Further functionalities planned

* **Prometheus exporter** - all activities handled by defguard will be exported to Prometheus so that you can create custom analytics and dashboards in your favorite tool from the Prometheus ecosystem
* Secure **SSH authentication based on OpenID Connect & Multi-Factor Authentication** with Defguard
* **SAML SSO Provider** - most _Old School_ enterprise systems implement SSO based on SAML v2 - so to provide our users with a full range of features this should be implemented
* 2FA/MFA functionality with **phone/SMS codes**
* **Password reset secured by MFA** (SMS/TOTP/Webauthn/...)
* **Mobile clients**
* **Simple DNS provider**





