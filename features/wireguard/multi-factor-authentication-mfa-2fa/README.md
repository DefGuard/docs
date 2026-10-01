---
description: >-
  Defguard is the sole VPN solution that genuinely implements Multi-Factor
  Authentication (MFA) before a WireGuard® VPN connection is established,
  significantly enhancing security against cyberattacks.
---

# Multi-Factor Authentication (MFA/2FA)

## TL;DR;

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

MFA is configured per VPN location. An administrator builds an **MFA flow**, an ordered list of steps where each step holds the factors allowed to satisfy it, and then enforces that flow on a location. The user proves one factor per step, in order, and the VPN tunnel is established only after the last step succeeds.

The available methods are:

* **Authenticator app** - a time-based one-time code (TOTP) from the user's authenticator app.
* **Email** - a one-time code sent to the user's email address.
* **OpenID** - a sign-in with a [cloud IdP/SSO provider](../../external-openid-providers/) such as Google, Microsoft, Okta or Jumpcloud.
* **Security key (FIDO2)** - a hardware security key registered in the user's profile.
* **Biometrics** - the biometric prompt on the user's enrolled mobile device.
* **Mobile Client** - a desktop connection approved from the user's enrolled mobile device by scanning a QR code.

Users manage their own authenticator app, email and security key factors in their Defguard profile, as described in [Setting up 2FA/MFA](../../../using-defguard-for-end-users/setting-up-2fa-mfa.md).

A location is not limited to one flow. It always has a default flow, and selected groups can be given a different one, so contractors and staff can face different requirements on the same VPN.

{% hint style="info" %}
Before 2.2, each location was set to either Internal MFA or External MFA, and that choice decided the available methods. Version 2.2 replaces the two modes with flows, so OpenID is now one method among the others and a single location can require, for example, an authenticator code followed by a mobile biometric confirmation.
{% endhint %}

Defguard also **supports multiple VPN locations (multiple VPNs), each of which can be configured independently** with its own MFA flow.

To set this up, start by [building a flow](mfa-flows-and-methods.md), then [enforce it on a location](configuring-mfa-for-a-location.md). Users connect as described in [Using Multi-Factor Authentication (MFA)](../../../using-defguard-for-end-users/desktop-client/using-multi-factor-authentication-mfa.md).

#### Multi device MFA

Some of Defguard's MFA methods are even more sophisticated, such as establishing a VPN connection using mobile biometric authentication in the desktop client. This is the **Mobile Client** method, and it requires:

User prerequisites (something a user has in terms of MFA terminology):

* A private WireGuard® key corresponding to the public key configured during the Defguard enrollment session.
* A mobile device successfully enrolled and added to the user profile (as a second VPN device).
* Private keys in the mobile device's secure key store, generated during the mobile device enrollment process, which are accessible only via the device's biometric authentication.

Extended MFA flow using two devices:

1. Scan the QR code displayed in the desktop app using the enrolled mobile device.
2. Perform MFA using the biometric authentication and private/public key pair, which is only accessible after successful biometric verification.
3. Only after these steps can the remaining Defguard flow, as described above, proceed.

## Why MFA for each connection Is not only Important but necessary

The main purpose of MFA is to strengthen security by acting as a highly effective barrier against cyberattacks such as phishing or brute-force attacks. With an effective MFA implementation, even if an attacker gains access to a user’s basic credentials (in WireGuard®’s case, typically the private key stored on the device), they will still be unable to connect to the VPN without the additional factor(s). This prevents access to critical private network resources and applications, blocking further exploitation and greatly reducing the risk of unauthorized access.

This means that relying on external SSO only for the initial device configuration is not sufficient to provide security in today’s environment. Even worse, marketing a VPN solution as providing MFA under these circumstances is highly misleading and potentially harmful to user security.
