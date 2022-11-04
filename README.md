# Intro to Defguard

![Network overview page displaying network usage statistics and info about connected users](.gitbook/assets/screen.png)

## What is Defguard?

Defguard is an open-source identity management system made with the aim to make company management as easy as possible. On this page you will find short description of our key features divided by enterprise and community edition.

### Community features:

* Wireguard VPN management
* Webhooks
* MFA
* Web3

### Enterprise features:

* OpenLDAP synchronization
* YubiKey Provisioning
* OpenID connect provider

## Problems that Defguard addresses and solves

* Your company has self hosted services such as: Git/Jira/Slack etc. and wants an easy to use central logging system with simple and nice UI
* Fast and easy to setup VPN (Remote access to company resources as above)
* Webhooks triggered after taking specified actions on user sending user data to provided URL which allows you to automate stuff like sending welcome e-mail or creating accounts in different services

## VPN management

Defguard allows you to setup a Wireguard VPN server in few simple steps. After that you can monitor your network by seeing overall usage and per user stats with information about all of their devices and download configuration files for new devices.

## Webhooks

Easy to use and configure outgoing webhooks triggered on actions taken on certain user to help you automate monotonous tasks.

## MFA
Multi-factor authentication is currently supported with the following methods:
* Time-based One-time Password Algorithm (TOTP - eg. Google Authenticator)
* WebAuthn/FIDO2 - for hardware key authentication support
* Web3 - authentication with software and hardware crypto wallets using Metamask and Wallet Connect

## OpenLDAP synchronization

In a few simple steps you can synchronize your OpenLDAP server and get following features:

* Read existing users
* Create new users
* Modify existing users
* Remove users
* Manage groups

## OpenID connect provider
With few simple steps you can add your self-hosted service to trusted OpenID client apps by Defguard and get Login with Defguard button on it.

## Web3
Web3 - community - integration with Web3/blockchain functionalities. Since defguard secures the whole user journey/connection, from secure communication using VPN, then authentication based on company identity and Multi-Factor Authentication (supporting crypto wallets),  it’s a perfect web2 -> web3 gateway for your organization.

* Wallet management - with Metamask and Wallet Connect - an easy way for users to validate the ownership of wallets - no need to send wallet addresses through slack/email anymore
* Organizational self-custody wallets (in development) - create a self-custody, multi-sig company wallet
* Blockchain transactions for organizations (in development) - since company members can securely connect/expose their wallets and the organization has a self-custody multi-signature 
wallet, the next step is to enable transactions on blockchain
* Blockchain node management (in development) - connect, monitor and manage your own blockchain nodes

## Guides: Jump right in

Follow our handy guides to get started on the basics as quickly as possible:

{% content-ref url="community-features/setting-up-your-instance/" %}
[setting-up-your-instance](community-features/setting-up-your-instance/)
{% endcontent-ref %}

{% content-ref url="community-features/wireguard/create-your-vpn-network.md" %}
[create-your-vpn-network.md](community-features/wireguard/create-your-vpn-network.md)
{% endcontent-ref %}

{% content-ref url="enterprise-features/ldap-synchronization-setup.md" %}
[ldap-synchronization-setup.md](enterprise-features/ldap-synchronization-setup.md)
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
