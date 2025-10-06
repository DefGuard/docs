---
description: >-
  Defguard is the sole VPN solution that genuinely implements Multi-Factor
  Authentication (MFA) before a WireGuard® VPN connection is established,
  significantly enhancing security against cyberattacks.
---

# Multi-Factor Authentication (MFA/2FA)

## TL;DR;&#x20;

* MFA (Multi-Factor Authentication) is a method of securing IT systems that requires the user to confirm their identity using at least two or more independent verification factors.
* MFA during a VPN connection requires the user to authenticate in the VPN client with two or more factors **before the connection can be established**.
* Defguard is the **only solution that enables MFA for WireGuard® VPN connections.**
* MFA is a widely overused marketing term for many (if not all) WireGuard®-based VPN solutions. In most other cases, it simply refers to **2FA for accessing the configuration panel or performing the initial client setup, and no MFA during connection stage**.

## What Multi-Factor Authentication actually is?

MFA (Multi-Factor Authentication) is a method of securing IT systems that requires the user to confirm their identity using at least two or more independent verification factors.

There are three main categories of MFA:

* Something a user knows: e.g., password, PIN, or answer to a security question.
* Something a user has: a physical token, smartphone, authenticator app generating one-time codes, or a security key.
* Something a user is: biometric data such as a fingerprint, face scan, or voice recognition

IT systems build authentication methods using those three areas and leverage them to secure operations done on the system (logging in into the system, establishing a connection, etc.).

## How Defguard handles MFA?

Defguard is a unique VPN solution that can be configured to use either:

1. [**internal**](internal-sso-based-mfa.md) **- based on** [**built-in IdP/SSO**](../../openid-connect/) - where users in Defguard profile manage their MFA methods (TOTP, Email, Mobile Biometry) and then use them to establish a VPN connection,
2. [**external**](external-sso-based-mfa.md) **- using** [**cloud IdP/SSO**](../../external-openid-providers/) **providers** such as [Google](../../external-openid-providers/google.md), [Microsoft](../../external-openid-providers/microsoft.md), [Okta](../../external-openid-providers/okta.md), [Jumpcloud](../../external-openid-providers/jumpcloud.md) (and others) to authorize each connection using those providers in Defguard desktop/mobile before the connection can be established.

In addition, when establishing a VPN connection, **Defguard enforces extra security measures** (including additional MFA steps in the user has category). It first securely establishes session keys (WireGuard® pre-shared keys), and only then configures the VPN location (our VPN gateway). The connection is possible to establish only with a device that has successfully passed the full authorization flow, enabling it to connect using its WireGuard® private/public keys and session keys.

Defguard also **supports multiple VPN locations (multiple VPNs), each of which can be configured independently to use either internal or external MFA**.

### Multi device MFA

Some of Defguard’s MFA methods are even more sophisticated, such as establishing a VPN connection using mobile biometric authentication in the desktop client. This method requires:

User prerequisites (something a user has in terms of MFA terminology):

* A private WireGuard® key corresponding to the public key configured during the Defguard enrollment session.
* A mobile device successfully enrolled and added to the user profile (as a second VPN device).
* Private keys in the mobile device’s secure key store, generated during the mobile device enrollment process, which are accessible only via the device’s biometric authentication.\


Extended MFA flow using two devices:

1. Scan the QR code displayed in the desktop app using the enrolled mobile device.
2. Perform MFA using the biometric authentication and private/public key pair, which is only accessible after successful biometric verification.
3. Only after these steps can the remaining Defguard flow, as described above, proceed.

## Why MFA for each connection Is not only Important but necessary

The main purpose of MFA is to strengthen security by acting as a highly effective barrier against cyberattacks such as phishing or brute-force attacks. With an effective MFA implementation, even if an attacker gains access to a user’s basic credentials (in WireGuard®’s case, typically the private key stored on the device), they will still be unable to connect to the VPN without the additional factor(s). This prevents access to critical private network resources and applications, blocking further exploitation and greatly reducing the risk of unauthorized access.



<mark style="color:$danger;">This means that relying on external SSO only for the initial device configuration is not sufficient to provide security in today’s environment. Even worse, marketing a VPN solution as providing MFA under these circumstances is highly misleading and potentially harmful to user security.</mark>

\
\




