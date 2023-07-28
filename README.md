# Defguard introduction

![](screencasts/defguard.gif)

Better quality video can [be found here to download](https://github.com/DefGuard/docs/raw/docs/screencasts/defguard-screencast.mkv)

## What is Defguard?

Defguard is an open-source security army knife: OpenID Identity & Wireguard VPN provider, Multi-Factor Authentication, Yubikey hardware provisioning & Web3 wallets in profile integration.

Building a secure organization has always been difficult and costly. Defguard provides a beautiful, easy-to-use (business users) and deploy (admin/DevOps) fundament to make your organization secure.

### **Features (implemented & production tested):**

* OpenID Connect Provider with easy UI for configuring OIDC apps
  * OpenLDAP synchronization
* Wireguard VPN Management
* Multi-Factor Authentication:
  * Time-based One-Time Password Algorithm (TOTP - e.g. Google Authenticator)
  * WebAuthn / FIDO2 - for hardware key authentication support
  * Web3 - authentication with crypto software and hardware wallets using Metamask, Wallet Connect, Ledger Extension
* [Yubikey hardware keys](https://www.yubico.com/) provisioning
* Webhooks
* Web3 wallet validation

### Features in development / planned

Go to product [**ROADMAP**](in-depth/roadmap.md)**.**

## Guides: Jump right in

Follow our handy guides to get started on the basics as quickly as possible:

{% content-ref url="features/setting-up-your-instance/" %}
[setting-up-your-instance](features/setting-up-your-instance/)
{% endcontent-ref %}

{% content-ref url="features/wireguard/create-your-vpn-network.md" %}
[create-your-vpn-network.md](features/wireguard/create-your-vpn-network.md)
{% endcontent-ref %}

{% content-ref url="features/ldap-synchronization-setup.md" %}
[ldap-synchronization-setup.md](features/ldap-synchronization-setup.md)
{% endcontent-ref %}

{% content-ref url="community-features/webhooks.md" %}
[webhooks.md](community-features/webhooks.md)
{% endcontent-ref %}

## Fundamentals: Dive a little deeper

Learn the fundamentals of Defguard to get a deeper understanding of our main features:

{% content-ref url="in-depth/architecture.md" %}
[architecture.md](in-depth/architecture.md)
{% endcontent-ref %}

{% content-ref url="in-depth/wireguard-vpn.md" %}
[wireguard-vpn.md](in-depth/wireguard-vpn.md)
{% endcontent-ref %}
