---
cover: .gitbook/assets/BG.png
coverY: 0
layout: landing
---

# Intro to Defguard

![Network overview page which shows statistics of network usage and info about connected users](.gitbook/assets/screen.png)

## What is Defguard?

Defguard is an open-source identity management system made with the aim to make company management as easy as possible. On this page you will find short description of our key features divided by enterprise and community edition.

### Community features:

* **Wireguard VPN management**
* **Webhooks**

### **Enterprise features:**

* LDAP synchronization
* Yubikey Provisioning
* OpenID connect provider

## Problems that Defguard adresses and solves

* Your company has self hosted services such as: Git/Jira/Slack etc. and wants an easy to use central logging system with simple and nice UI
* Fast and easy to setup VPN (Remote access to company resources as above)
* Webhooks triggered after taking specified actions on user sending user data to provided url which allows you to automate stuff like sending welcome e-mail or creating accounts in different services

## VPN management

Defguard allows you to setup a Wireguard VPN server in few simple steps. After that you can monitor your network by seeing overall usage and per user stats with information about all of their devices and download configuration files for new devices.

## Webhooks

Easy to use and configure outgoing webhooks triggered on actions taken on certain user to help you automate monotonious tasks.

## LDAP synchronization

In a few simple steps you can synchronize your LDAP data for example import your ldap data to defguard and:

* Create new users
* Modify existing users
* Manage groups

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
