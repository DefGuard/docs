# Intro to Defguard

![Network overview page displaying network usage statistics and info about connected users](.gitbook/assets/screen.png)

## What is Defguard?

Defguard is an open-source security army knife (Identity, MFA, VPN, Yubikey, Web3).

Building a secure organization has always been difficult and costly. Defguard provides a beautiful, easy-to-use (business users) and deploy (admin/DevOps) fundament to make your organization secure.

**Features:**

* OpenID Connect provider (with OpenLDAP synchronization)
* Wireguard  VPN Management
* Multi-Factor Authentication:
  * Time-based One-Time Password Algorithm (TOTP - e.g. Google Authenticator)
  * WebAuthn / FIDO2 - for hardware key authentication support
  * Web3 - authentication with crypto software and hardware wallets using Metamask, Wallet Connect, Ledger Extension
* [Yubikey hardware keys](https://www.yubico.com/) provisioning
* Webhooks
* Web3 wallet validation

## OpenID Connect provider

With a few simple steps, you can add your self-hosted service to trusted OpenID client apps by Defguard and get log in with  Defguard button on it.

## Multi-Factor Authentication

MFA adds another security layer to your OpenID setup. Currently there are supported  the following methods:

* Time-based One-time Password Algorithm (TOTP - eg. Google Authenticator)
* WebAuthn/FIDO2 - for hardware key authentication support
* Web3 - authentication with software and hardware crypto wallets using Metamask and Wallet Connect

## VPN management

Defguard allows you to set up a Wireguard VPN server in a few simple steps. After that, you can monitor your network by seeing overall usage and per-user stats with information about all of their devices and download configuration files for new devices.

## Webhooks

Easy to use and configure outgoing webhooks triggered on actions taken on certain user to help you automate monotonous tasks.

## OpenLDAP synchronization

In a few simple steps you can synchronize your OpenLDAP server and get following features:

* Read existing users
* Create new users
* Modify existing users
* Remove users
* Manage groups

## Web3

Web3 - integration with Web3/blockchain functionalities. Since defguard secures the whole user journey/connection, from secure communication using VPN, then authentication based on company identity and Multi-Factor Authentication (supporting crypto wallets), it’s a perfect web2 -> web3 gateway for your organization.

* Wallet management - with Metamask and Wallet Connect - an easy way for users to validate the ownership of wallets - no need to send wallet addresses through slack/email anymore
* Organizational self-custody wallets (in development) - create a self-custody, multi-sig Company wallet
* Blockchain transactions for organizations (in development) - since company members can securely connect/expose their wallets and the organization has a self-custody multi-signature wallet, the next step is to enable transactions on blockchain
* Blockchain node management (in development) - connect, monitor and manage your own blockchain nodes

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
