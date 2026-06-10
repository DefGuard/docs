---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/notifications/setting-up-smtp-for-email-notifications
---

# Email notifications

## Configuration

In **Settings → Notifications → SMTP Configuration** you can setup a connection to an SMTP server.

<figure><img src="../../.gitbook/assets/image (256).png" alt=""><figcaption></figcaption></figure>

This enables your Defguard instance to send **email notifications** to users and admins.

After you fill in all the relevant fields you can use the **Send test email** form at the bottom to test if your configuration is correct.



## Possible notifications

* **Welcome message after enrollment**
  * welcome message sent to the user after completing enrollment and activating the account
* **Support data**
  * diagnostic data sent manually by an admin to Defguard support
* **Desktop client configuration**
  * configuration link sent when an admin starts desktop client configuration for a user
* **User enrollment**
  * enrollment link and token sent to user when admin triggers it in Defguard
* **New device added to your account**
  * notification sent when a new device is added to the user's account
* **New device logged in to your account**
  * notification sent when logging in from a new, unrecognized device/browser
* **New login to OIDC application**
  * notification sent on the first login to an OpenID application
* **Multi-Factor Authentication activation**
  * activation code sent when the user starts email MFA configuration
* **Multi-Factor Authentication {method} has been activated**
  * confirmation that an MFA method has been activated
* **Multi-Factor Authentication code for login**
  * MFA code sent on request during login
* **Password reset**
  * password reset link sent when an admin initiates a reset or the user [requests one in Edge](../../using-defguard-for-end-users/changing-your-password.md)
* **Password reset success**
  * confirmation sent after a successful password reset
* **User import blocked**
  * alert to admins when user import from LDAP/directory is blocked
* **User enrollment completed**
  * notification to an admin when a user completes enrollment
* **Automatic Let's Encrypt certificate refresh failed**
  * alert to admins when the automatic Let's Encrypt certificate renewal fails
* **Certificate expiration**
  * warning to admins when a custom-uploaded HTTPS/TLS certificate (Core or Edge) is approaching expiration
* **Certificate has expired**
  * alert to admins when a custom-uploaded HTTPS/TLS certificate (Core or Edge) has already expired
