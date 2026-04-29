---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/about/features-overview
---

# Features overview

### Remote Access with WireGuard® VPN 2FA/MFA:

* [**Multi-Factor Authentication**](../features/wireguard/multi-factor-authentication-mfa-2fa/) using our [desktop client](https://defguard.net/client)
* **Multiple VPN Locations** (networks/sites) - with defined access (all users or only Admin group)
* Multiple [Gateways](https://github.com/DefGuard/gateway) for each VPN Location ([**high availability/failover**](../deployment-strategies/high-availability-and-failover/)) - supported on a cluster of routers/firewalls for Linux, FreeBSD/PFSense/OPNSense
* Import your current WireGuard server configuration (with a wizard!)
* _Easy_ device setup by users themselves (self-service)
* Automatic IP allocation
* Kernel (Linux, FreeBSD/OPNSense/PFSense) and userspace WireGuard support
* [Dashboard and statistics overview](../features/wireguard/network-overview.md) of connected users/devices for admins

_Defguard is not an official WireGuard project, and WireGuard is a registered trademark of Jason A. Donenfeld._

### [Activity and audit Logs](../features/activity-log/)

* User event logging with detailed metadata
* Advanced filtering and search by user, module, event type and time range
* Role-based visibility - users can see only their events
* Grouped logs by modules (Defguard, enrollment, VPN)
* Real-time [log streaming](../features/activity-log/activity-log-streaming/) to SIEM tools (Enterprise feature)

### OpenID Connect

* Defguard is an internal OIDC provider for [Single Sign-On](../features/openid-connect/).
* Supports [external OpenID](../features/external-openid-providers/) providers for user authentication.

### [Access Control List](../features/access-control-list/)

* Access rules for VPN locations
* Allow or deny access based on users or groups
* Changes are applied in **real time**

### Identity Management:

* [**OpenID Connect**](https://openid.net/developers/how-connect-works/) **based SSO**
* External [OpenID providers for login/account creation (Google/Microsoft/Custom)](../features/external-openid-providers/)
* LDAP (tested on [OpenLDAP](https://www.openldap.org/)) synchronization
* Nice UI to manage users
* Users **self-service** (besides typical data management, users can revoke access to granted apps, MFA, WireGuard, etc.)

### [Multi-Factor/2FA](https://en.wikipedia.org/wiki/Multi-factor_authentication) Authentication

* [Time-based One-Time Password Algorithm](https://en.wikipedia.org/wiki/Time-based_one-time_password) (TOTP - e.g. Google Authenticator)
* WebAuthn / FIDO2 - for hardware key authentication support (e.g. YubiKey, Face ID, Touch ID, ...)
* Email tokens

### Account Lifecycle Management:

* Secure remote (over the internet) [user enrollment](https://defguard.gitbook.io/defguard/help/remote-user-enrollment)
* User [onboarding after enrollment](https://defguard.gitbook.io/defguard/help/remote-user-enrollment/user-onboarding-after-enrollment)
* Self-service for password reset

### Notifications

* [Email notifications ](../features/notifications/setting-up-smtp-for-email-notifications.md)via SMTP
* [Gateway disconnect/reconnect](../features/notifications/gateway-notifications.md) notifications
* [New version](../features/notifications/new-version-notifications.md) notifications

### YubiKey Provisioning

[YubiKey hardware keys](https://www.yubico.com/) provisioning for users with _one click_.

### Integrations

[Webhooks](../features/integrations/webhooks.md) & [REST API](../features/integrations/api-tokens.md)

Build with [Rust](https://www.rust-lang.org/) for portability, security, and speed.
