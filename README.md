# Introduction

{% embed url="https://www.youtube.com/watch?v=4PF7edMGBwk" %}

## What is Defguard?

Defguard is a **comprehensive Remote Access Management solution** incorporating in one solution:

* Remote Access secured by [WireGuard® VPN with 2FA/Multi-Factor Authentication](admin-and-features/features-and-configuration/wireguard/),
* Identity Management with [SSO based on OpenID Identity Provider](admin-and-features/features-and-configuration/openid-connect/),
* Account Lifecycle management with [secure remote account onboarding](help/enrollment.md).

It's a **security platform** for building **secure** and **privacy-aware organizations,** as we put great emphasis not only on functionality but also on secure code, architecture and testing (application and security).

By design **defguard core (the main component) is meant to be deployed in your secure network segments** (available only from an internal network or by VPN) and operations that require public access (like user onboarding, enrollment, password reset, etc.) **are done using a secure proxy:**

<figure><img src=".gitbook/assets/defguard-architecture.png" alt=""><figcaption><p>defguard architecture</p></figcaption></figure>

This approach is vastly different from most (if not all) VPN/IdP solutions, which are a simple or monolithic application focus on functionalities (like generating configs, managing users, etc.) and most of the time is publicly available in the Internet for any attacker.

Incorporating IDM, ALM, VPN has also other advantages:

1. Internal IdP with 2FA/MFA enables us to provide [**real VPN 2FA/MFA**](admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/architecture.md) - and not like most applications just 2FA when opening the app (and not during the connection process). Even if you use [external OIDC](enterprise/external-openid-providers.md) (Google/Microsoft/Custom - which defguard supports), we still use our internal IdP for 2FA/MFA.
2. Your organisation may use just **one account** (login) for access control to all your applications as well as VPN.
3. It simplifies deployment, maintenance, audits.

### What does it mean to build a secure "organization"?

First of all, It means implementing a **secure architecture** for your network and systems. In the age of "cloud," all systems (and data) are **public.** But that should be **for you to decide!**

That's why defguard architecture (and implementation) is secure (and thoroughly and comprehensively audited by one of the best security researchers). If you want full privacy, defguard only exposes its VPN gateway (to provide a secure channel to all internal systems that should be behind the firewall) and the public proxy (for the remote user enrollment process).

It also means having **fundamental secure processes,** like:

* secure remote user enrollment (self-service)
* user self-service to manage their own data, change passwords, add/remove VPN devices, connect securely to networks
* for administrators to easily setup, manage and monitor multiple VPN networks (with access control) to provide a secure connection to applications that should not be visible on the internet
* deploy an Identity Provider to have one place to manage all users
* that Identity Provider should provide SSO functionality to enable users to log in to all systems with one login/password
  * have 2FA/MFA functionality to harden security
* setup Yubikey Hardware keys to enable the best 2FA security, secure SSH login with private keys on a secure hardware
* integrate all your systems with API, and Webhooks (to access defguard functionalities or users' data)

**Building a secure organization has always been difficult and costly. Defguard provides a beautiful, easy-to-use (business users) and deploy (admin/DevOps) fundament to make your organization secure.**

## Features

### Remote Access with WireGuard® VPN 2FA/MFA:

* [**Multi-Factor Authentication**](admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/) using our [desktop client](https://defguard.net/client)
* **multiple VPN Locations** (networks/sites) - with defined access (all users or only Admin group)
* multiple [Gateways](https://github.com/DefGuard/gateway) for each VPN Location (**high availability/failover**) - supported on a cluster of routers/firewalls for Linux, FreeBSD/PFSense/OPNSense
* import your current WireGuard server configuration (with a wizard!)
* _easy_ device setup by users themselves (self-service)
* automatic IP allocation
* kernel (Linux, FreeBSD/OPNSense/PFSense) & userspace WireGuard support
* dashboard and statistics overview of connected users/devices for admins

_defguard is not an official WireGuard project, and WireGuard is a registered trademark of Jason A. Donenfeld._

### Identity Management:

* ### [OpenID Connect](https://openid.net/developers/how-connect-works/) based SSO
* External OpenID privoders for login/account creation (Google/Microsoft/Custom)
* LDAP (tested on [OpenLDAP](https://www.openldap.org/)) synchronization
* nice UI to manage users
* Users **self-service** (besides typical data management, users can revoke access to granted apps, MFA, Wireguard, etc.)

#### [Multi-Factor/2FA](https://en.wikipedia.org/wiki/Multi-factor\_authentication) Authentication

* [Time-based One-Time Password Algorithm](https://en.wikipedia.org/wiki/Time-based\_one-time\_password) (TOTP - e.g. Google Authenticator)
* WebAuthn / FIDO2 - for hardware key authentication support (eg. YubiKey, FaceID, TouchID, ...)
* Email tokens

### Account Lifecycle Management:

* Secure remote (over the internet) [user enrollment](https://defguard.gitbook.io/defguard/help/remote-user-enrollment)
* User [onboarding after enrollment](https://defguard.gitbook.io/defguard/help/remote-user-enrollment/user-onboarding-after-enrollment)
* Self-service for password reset&#x20;

### Yubikey Provisioning

[Yubikey hardware keys](https://www.yubico.com/) provisioning for users with _one click_

### Integrations

Webhooks & REST API

Build with [Rust](https://www.rust-lang.org/) for portability, security, and speed

### Pentested!

**Checked by professional security researchers** (see [comprehensive security report](https://defguard.net/images/decap/isec-defguard.pdf))

## Guides: Jump right in

Follow our handy guides to get started on the basics as quickly as possible:

{% content-ref url="features/setting-up-your-instance/" %}
[setting-up-your-instance](features/setting-up-your-instance/)
{% endcontent-ref %}

{% content-ref url="admin-and-features/features-and-configuration/wireguard/create-your-vpn-network.md" %}
[create-your-vpn-network.md](admin-and-features/features-and-configuration/wireguard/create-your-vpn-network.md)
{% endcontent-ref %}

{% content-ref url="admin-and-features/features-and-configuration/ldap-synchronization-setup/" %}
[ldap-synchronization-setup](admin-and-features/features-and-configuration/ldap-synchronization-setup/)
{% endcontent-ref %}

{% content-ref url="admin-and-features/features-and-configuration/webhooks.md" %}
[webhooks.md](admin-and-features/features-and-configuration/webhooks.md)
{% endcontent-ref %}

{% content-ref url="help/desktop-client.md" %}
[desktop-client.md](help/desktop-client.md)
{% endcontent-ref %}

## Fundamentals: Dive a little deeper

Learn the fundamentals of Defguard to get a deeper understanding of our main features:

{% content-ref url="in-depth/architecture.md" %}
[architecture.md](in-depth/architecture.md)
{% endcontent-ref %}
