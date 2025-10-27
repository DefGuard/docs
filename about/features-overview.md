# Features overview

Defguard combines secure remote access, modern identity management, and powerful integrations — all in one open-source platform.\
Below you’ll find an overview of its main capabilities, designed for both administrators and end users.

### 🌐 Remote Access with WireGuard® VPN + 2FA/MFA

Secure, high-performance VPN built on [WireGuard®](https://www.wireguard.com/) protocol, enhanced with real multi-factor authentication.

* [Multi-Factor Authentication](https://docs.defguard.net/features/wireguard/multi-factor-authentication-mfa-2fa) using our [desktop client](https://defguard.net/client)
* Multiple VPN Locations (networks/sites) - define access for all users or selected admin groups
* Multiple [Gateways](https://github.com/DefGuard/gateway) per VPN Location with [high availability/failover](https://docs.defguard.net/deployment-strategies/high-availability-and-failover)
* Import your existing WireGuard configuration easily with a guided wizard
* Self-service device setup - users can add their devices on their own
* Automatic IP allocation for connected devices
* Kernel (Linux, FreeBSD/OPNSense/PFSense) & userspace WireGuard support
* [Dashboard & statistics](https://docs.defguard.net/features/wireguard/network-overview) for admins - track users and connections

### 💻 Desktop, 📱 Mobile & 🧰 CLI Clients

Defguard provides modern, easy-to-use clients for every platform - giving users secure, MFA-protected VPN access wherever they work.

* Desktop Client - available for [Windows, macOS, and Linux](https://defguard.net/download/)
  * Enables direct VPN connection using MFA/2FA
  * One-click enrollment via secure deep links received from the administrator
* Mobile Apps - available for [Android](https://play.google.com/store/apps/details?id=net.defguard.mobile) and [iOS](https://apps.apple.com/us/app/defguard-vpn-client/id6748068630)
  * Connect securely to the Defguard VPN using multi-factor authentication
  * Includes an additional biometric factor (Face ID / Touch ID) for MFA confirmation
  * Allows fast approval of authentication requests directly from the phone
* [CLI Client](../using-defguard-for-end-users/cli-client.md) - lightweight and script-friendly tool for Linux
  * Provides full VPN control via terminal
  * Ideal for automation, servers, or advanced users preferring CLI workflows

### 🔑 Multi-Factor/2FA Authentication

Add another layer of protection to user accounts.

* [Time-based One-Time Password (TOTP)](https://en.wikipedia.org/wiki/Time-based_one-time_password) - compatible with Google Authenticator, Authy, etc.
* WebAuthn / FIDO2 - hardware keys, Face ID, Touch ID, and other authenticators
* Email tokens as an additional authentication method
* Biometric verification via the mobile app - use your device’s built-in Face ID or fingerprint sensor to confirm login or VPN access

### 👤 Identity Management

Manage your users and their access in one place.

* [OpenID Connect](https://openid.net/developers/how-connect-works/) based SSO
* External [OpenID providers for login/account creation (Google/Microsoft/Custom)](https://docs.defguard.net/features/external-openid-providers)
* [LDAP and Active Directory integration](../features/ldap-and-active-directory-integration/)
* Simple, modern UI for managing users
* User self-service - manage data, revoke app access, reset MFA, control WireGuard devices

### 🧭 Account Lifecycle Management

Automated, secure, and user-friendly onboarding.

* Secure remote (over the Internet) [user enrollment](../features/remote-user-enrollment/)
* [User onboarding after enrollment](../features/remote-user-enrollment/user-onboarding-after-enrollment.md)
* Self-service password reset

### 🧱 Access Control List

Granular, instant control over VPN access.

* [Access rules per VPN location](../features/access-control-list/)
* Allow or deny access based on users or groups
* Changes are applied in real time

### 🔐 OpenID Connect

Defguard acts as your internal OIDC provider - giving you full control over identity and SSO.

* Defguard is an [internal OIDC provider](../features/openid-connect/) for Single Sign-On
* Supports [external OpenID](https://docs.defguard.net/features/external-openid-providers) providers for authentication

### 🧾 Activity & Audit Logs

Monitor and understand what’s happening across your system with detailed, searchable logs.

* [User event logging](../features/activity-log/#viewing-activity-log-events) with complete metadata
* Advanced filtering by user, module, event type, or time range
* Role-based visibility - users only see their own events
* Logs grouped by module (Defguard, enrollment, VPN)
* Real-time [log streaming](https://docs.defguard.net/features/activity-log/activity-log-streaming) to SIEM tools _(Enterprise feature)_

### 📬 Notifications

Stay in the loop with real-time notifications.

* [Email notifications](https://docs.defguard.net/features/notifications/setting-up-smtp-for-email-notifications) via SMTP
* [Gateway disconnect/reconnect](https://docs.defguard.net/features/notifications/gateway-notifications) alerts
* [New version](https://docs.defguard.net/features/notifications/new-version-notifications) notifications

### 🛡️ YubiKey Provisioning

Easily create and populate the SSH and GPG/OpenPGP keys on a YubiKey hardware key.

* [YubiKey hardware keys provisioning](../features/yubikey-provisioning.md) for users with one click

### 🔗 Integrations

Easily connect Defguard with your existing systems.

* [Webhooks](https://docs.defguard.net/features/integrations/webhooks) for automation
* [REST API](https://docs.defguard.net/features/integrations/api-tokens) for integrations and scripting

### ⚙️ Built with Rust

Built in [Rust](https://www.rust-lang.org/) - delivering portability, security, and speed from the ground up.
