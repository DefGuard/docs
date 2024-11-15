# Security concepts

defguard's **fundamental secure processes**:

* secure remote user enrollment (self-service)
* user self-service to manage their own data, change passwords, add/remove VPN devices, connect securely to networks
* for administrators to easily setup, manage and monitor multiple VPN networks (with access control) to provide a secure connection to applications that should not be visible on the internet
* deploy an Identity Provider to have one place to manage all users
* that Identity Provider should provide SSO functionality to enable users to log in to all systems with one login/password
  * have 2FA/MFA functionality to harden security
* setup Yubikey Hardware keys to enable the best 2FA security, secure SSH login with private keys on a secure hardware
* integrate all your systems with API, and Webhooks (to access defguard functionalities or users' data)
