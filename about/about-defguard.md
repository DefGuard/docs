# About Defguard

## What is Defguard?

Defguard is an enterprise-ready platform built on top of WireGuard®, designed to make private networking simple, scalable, and secure.

It integrates identity management, policy enforcement, and secure access provisioning into a single, cohesive system - whether you deploy it in your own infrastructure or in the cloud.

Defguard helps organizations:

* Manage VPN access for distributed teams.
* Integrate identity sources such as LDAP, Active Directory, or external OIDC providers.
* Enforce multi-factor authentication (MFA).
* Automate device enrollment.
* Simplify network segmentation and access control using policies.

For a detailed list of features go to the [Features overview](/broken/pages/dt2GFcP4faJtj82iTYQw) section.

## Why choose Defguard?

Defguard was built with security, transparency, and control at its core.

Here’s why organizations choose it over traditional VPN management or proprietary systems:

#### 🏗️ Self-Hosted and Privacy-Focused

Defguard can be deployed on your own infrastructure, giving you full ownership of data and keys.

No external cloud relay, no hidden telemetry - your traffic and user data never leave your environment.

#### 🔒 Zero Trust by Design

Authentication and authorization with MFA happen continuously, not just at login.&#x20;

Access decisions are policy-driven and identity-based, reducing lateral movement risks and insider threats.

#### 🔑 True MFA (Multi-Factor Authentication) for VPN Access

Most applications provide MFA only when opening or logging into the app - not during the VPN connection itself.

Defguard takes a different approach.

Thanks to its internal Identity Provider (IdP), Defguard enforces real, [connection-level MFA](../in-depth/architecture/architecture.md), ensuring that multi-factor authentication is applied as part of the VPN handshake - not just the UI login step.

Even when using [external OIDC](../features/external-openid-providers/) providers (Google, Microsoft, or a custom one supported by Defguard), Defguard still applies its internal IdP-based MFA for actual VPN session authentication.

This design delivers true, end-to-end verification that protects both users and infrastructure from credential theft or token replay attacks.

#### ⚙️ Open, Extensible, and Interoperable

Defguard integrates cleanly with your existing identity stack - LDAP, AD, or OIDC - without forcing vendor lock-in.

Its webhooks make it easy to extend or automate within DevOps workflows.

#### 🧭 Simple for Users, Powerful for Admins

End users enjoy one-click VPN access via the Defguard apps, while admins gain granular control through a modern web interface.

#### 🧩 Modular and Scalable

Each component (Core, Gateway, Proxy) can be deployed independently, allowing flexible scaling - from a single office setup to multi-region enterprise deployments.

#### 🧱 Security Built into the Development Process

Defguard follows modern software supply-chain and security best practices:

* Signed container images and binaries.
* Publishing SBOMs.
* Scanning for and reacting to vulnerabilities on a daily basis.
* Regular penetration testing.

#### 🌱 Open Source and Transparent by Design

Defguard is open source - you can inspect, audit, and contribute to the code that powers your infrastructure.

We believe transparency is a cornerstone of security and trust.

Our approach goes beyond code:

* Open organisation: we share our [development roadmap](https://github.com/orgs/DefGuard/projects/5), [security practices](https://defguard.net/security/), and [architecture decisions](../in-depth/architecture-decision-records/) publicly.
* Open processes: our builds, signing, and release workflows are verifiable end-to-end.
* Open security scans: we publish [penetration tests reports](https://defguard.net/pentesting/), and [daily SBOM CVE scan reports](https://defguard.net/sbom/).
* Community-driven development: [we welcome feedback](../support.md#found-a-bug-need-a-feature), issues, and [contributions from users](../for-developers/contributing.md) and integrators.
* No black boxes: every component, from Core to Gateway, can be deployed, configured, and verified independently.

Defguard’s openness ensures trust through verifiability, not promises — aligning with the principles of modern, transparent cybersecurity.

## How is Defguard built?

Defguard consists of several modular services that can be deployed together or separately, depending on your architecture:

* Core – the main service responsible for user management, authentication, configuration storage, and integrations.
* VPN Gateway – provides the VPN endpoint for clients; runs WireGuard and synchronizes configuration with Core.
* Public Proxy – an optional component that handles communication between the Core and external services or clients in restricted environments.
* Desktop and Mobile Apps – client applications for macOS, Windows, Linux, Android, and iOS, allowing users to connect easily and securely.

This layered architecture with a strict division of responsibility guarantees maximum security (verified by multiple in-depth [penetration tests](https://defguard.net/pentesting/)).

<figure><img src="../.gitbook/assets/Untitled Diagram.drawio (1).png" alt=""><figcaption></figcaption></figure>

For the reasoning behind this division, please refer to the [Architecture documentation](../in-depth/architecture/).
