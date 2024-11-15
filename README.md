---
icon: hand-wave
---

# Introduction

{% embed url="https://www.youtube.com/watch?v=4PF7edMGBwk" %}

## What is Defguard?

Defguard is a **comprehensive Remote Access Management solution** incorporating in one solution:

* Remote Access secured by [WireGuard® VPN with 2FA/Multi-Factor Authentication](admin-and-features/features-and-configuration/wireguard/),
* Identity Management with [SSO based on OpenID Identity Provider](admin-and-features/features-and-configuration/openid-connect/),
* Account Lifecycle management with [secure remote account onboarding](help/enrollment.md).

***

<mark style="color:purple;">**Our primary focus at defguard is on prioritizing security. Then, we aim to make this challenging topic both useful and as easy to navigate as possible.**</mark>

***

Having said that, this security platform is for building **secure** and **privacy-aware organizations,** as we put great effort not only on functionality but first and foremost on secure code, architecture and testing (application and security).

### Basic security concept

<figure><img src=".gitbook/assets/defguard (1).png" alt=""><figcaption><p>Defguard main architecture concept</p></figcaption></figure>

The main architecture concept is that **all critical data should be in the internal (Intranet) network and not exposed in the public Internet** (contrary to typical and common cloud approach).

This approach is **vastly different from most (if not all) VPN/IdP solutions**, which are a simple or monolithic applications focus on functionalities and most of the time is publicly available in the Internet for any attacker to exploit.

Of course you can deploy defguard in a typical scenario (all services on one server and even all publicly available) - but that should be **for you to decide!**

### Incorporating IdP and VPN in one solution

Incorporating IDM, ALM, VPN has also other advantages:

1. Internal IdP with 2FA/MFA enables us to provide [**real VPN 2FA/MFA**](admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/architecture.md) - and not like most applications just 2FA when opening the app (and not during the connection process). Even if you use [external OIDC](enterprise/all-enteprise-features/external-openid-providers.md) (Google/Microsoft/Custom - which defguard supports), we still use our internal IdP for 2FA/MFA.
2. Your organization may use just **one account** (login) for access control to all your applications as well as VPN.
3. It simplifies deployment, maintenance, audits.

More about [defguard's architecture and security can be found here](in-depth/architecture/).

## Features

### Remote Access with WireGuard® VPN 2FA/MFA:

* [**Multi-Factor Authentication**](admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/) using our [desktop client](https://defguard.net/client)
* **multiple VPN Locations** (networks/sites) - with defined access (all users or only Admin group)
* multiple [Gateways](https://github.com/DefGuard/gateway) for each VPN Location ([**high availability/failove**](admin-and-features/setting-up-your-instance/high-availability-and-failover.md)**r**) - supported on a cluster of routers/firewalls for Linux, FreeBSD/PFSense/OPNSense
* import your current WireGuard server configuration (with a wizard!)
* _easy_ device setup by users themselves (self-service)
* automatic IP allocation
* kernel (Linux, FreeBSD/OPNSense/PFSense) & userspace WireGuard support
* dashboard and statistics overview of connected users/devices for admins

_defguard is not an official WireGuard project, and WireGuard is a registered trademark of Jason A. Donenfeld._

### Identity Management:

* ### [OpenID Connect](https://openid.net/developers/how-connect-works/) based SSO
* External [OpenID providers for login/account creation (Google/Microsoft/Custom)](enterprise/all-enteprise-features/external-openid-providers.md)
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

**Checked by professional security researchers** (see [comprehensive security report](https://defguard.net/pdf/isec-defguard.pdf))

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

{% content-ref url="in-depth/architecture/" %}
[architecture](in-depth/architecture/)
{% endcontent-ref %}
