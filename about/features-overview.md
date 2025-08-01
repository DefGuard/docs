# Features overview

### Remote Access with WireGuard® VPN 2FA/MFA:

* [**Multi-Factor Authentication**](../admin-and-features/wireguard/multi-factor-authentication-mfa-2fa/) using our [desktop client](https://defguard.net/client)
* **Multiple VPN Locations** (networks/sites) - with defined access (all users or only Admin group)
* Multiple [Gateways](https://github.com/DefGuard/gateway) for each VPN Location ([**high availability/failove**](../deployment-strategies/high-availability-and-failover.md)**r**) - supported on a cluster of routers/firewalls for Linux, FreeBSD/PFSense/OPNSense
* Import your current WireGuard server configuration (with a wizard!)
* _Easy_ device setup by users themselves (self-service)
* Automatic IP allocation
* Kernel (Linux, FreeBSD/OPNSense/PFSense) & userspace WireGuard support
* [Dashboard and statistics overview](../admin-and-features/wireguard/network-overview.md) of connected users/devices for admins

_Defguard is not an official WireGuard project, and WireGuard is a registered trademark of Jason A. Donenfeld._

### Identity Management:

* #### [OpenID Connect](https://openid.net/developers/how-connect-works/) based SSO
* External [OpenID providers for login/account creation (Google/Microsoft/Custom)](../admin-and-features/external-openid-providers/)
* LDAP (tested on [OpenLDAP](https://www.openldap.org/)) synchronization
* Nice UI to manage users
* Users **self-service** (besides typical data management, users can revoke access to granted apps, MFA, WireGuard, etc.)

#### [Multi-Factor/2FA](https://en.wikipedia.org/wiki/Multi-factor_authentication) Authentication

* [Time-based One-Time Password Algorithm](https://en.wikipedia.org/wiki/Time-based_one-time_password) (TOTP - e.g. Google Authenticator)
* WebAuthn / FIDO2 - for hardware key authentication support (eg. YubiKey, FaceID, TouchID, ...)
* Email tokens

### Account Lifecycle Management:

* Secure remote (over the internet) [user enrollment](https://defguard.gitbook.io/defguard/help/remote-user-enrollment)
* User [onboarding after enrollment](https://defguard.gitbook.io/defguard/help/remote-user-enrollment/user-onboarding-after-enrollment)
* Self-service for password reset

### YubiKey Provisioning

[YubiKey hardware keys](https://www.yubico.com/) provisioning for users with _one click_

### Integrations

Webhooks & REST API

Build with [Rust](https://www.rust-lang.org/) for portability, security, and speed
