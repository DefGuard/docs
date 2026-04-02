---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/in-depth/architecture/security-concepts
---

# Security concepts

Defguard's **fundamental secure processes**:

* Secure remote user enrollment (self-service)
* User self-service to manage their own data, change passwords, add/remove VPN devices, connect securely to networks
* For administrators to easily setup, manage and monitor multiple VPN networks (with access control) to provide a secure connection to applications that should not be visible on the internet
* Deploy an Identity Provider to have one place to manage all users
* That Identity Provider should provide SSO functionality to enable users to log in to all systems with one login/password
  * Have 2FA/MFA functionality to harden security
* Setup YubiKey Hardware keys to enable the best 2FA security, secure SSH login with private keys on a secure hardware
* Integrate all your systems with API, and Webhooks (to access Defguard functionalities or users' data)
