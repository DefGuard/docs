---
icon: comment-question
description: Here are some common issues / problems that are frequently encountered.
---

# Troubleshooting Guide

{% hint style="info" %}
Before contacting support, please see if the answer cannot be found here:
{% endhint %}

## Desktop client real-time/auto sync doesn't work&#x20;

The client communicates with core to initiate the handshake through the secure proxy (which is also the enrollment service - [more details about the architecture here](../wireguard/multi-factor-authentication-mfa-2fa/architecture.md)), so it's critical it works and is properly configured.

Common problems may be:

1. proxy / enrollment isn't working
2. DEFGUARD\_ENROLLMENT\_URL is set incorrectly in core - if set incorrectly the client will try to connect to default URL which is "http://localhost:8080" and MFA will not work.

## Click on "Use authenticator app" in client and nothing happens

See the "[Desktop client real-time/auto sync doesn't work](./#desktop-client-real-time-auto-sync-doesnt-work)" answer.

## TOTP / Email codes for MFA do not work

If you are having problems with TOTP codes form 2FA/MFA (when logging in to defguard or when connecting to VPN) please make sure your clock on the server that defguard core is running is set properly.

Best would be to setup on the server NTP time synchronization.

## How to check if the VPN is working / my VPN is not working!

If you have configured your defguard instance correctly, after connecting to the VPN you should be able to ping your VPN server, for example if you have the following setup:

<figure><img src="../../.gitbook/assets/Screenshot 2024-03-24 at 18.36.43.png" alt="" width="313"><figcaption><p>Example VPN server IP</p></figcaption></figure>

after connecting to VPN you should be able to ping: 10.1.1.1.

### VPN Location settings changed

Check if the VPN location configuration has changed. If it did and you do not have Enterprise license where real-time config sync is automatic, the user needs to [update their client configuration by updating that instance manually.](../../help/configuring-vpn/add-new-instance/update-instance.md)

### Conflicting networks

If you are **not able to ping the VPN server** the **most common problem is that you have chosen a network that may be in conflict with your other networks** (local network, network from your Internet provider, etc.).

To examine your routing use on Mac and Linux `netstat -rn` command. Let's look at the example from above, the VPN network is: 10.1.1.0/24, let's look at the network route tabile:

```
root# netstat -rn                                                                                                                                          ✔  18:40:46  
Routing tables

Internet:
Destination        Gateway            Flags               Netif Expire
default            10.123.123.1.      UGScg                 en0
10.10.0/24         link#13            UCS                   en0      !
10.0.0.0/8         link#13            UCS                   en0      !
10.1.1.0/24        10.1.1.1.          UHLWI                 utun6  1007
```

In the example above you can see that the whole 10.0.0.0 network (with mask 255.0.0.0 eg /8) is routed through default device en0.

{% hint style="danger" %}
Because of the main routing 10.0.0.0/8 the VPN server routing the network 10.1.1.0/24 (which is included in 10.0.0.0/8) will not work.
{% endhint %}

### Firewall rulles

Another common problem is that **your server on which the gateway is working, has some firewall rules that interfere with VPN network.** Please examine carefully  `ufw` and `iptables` (even if ufw is disabled there may be `iptables` rules).

### VPN location ACLs

If a user has no access to the VPN location, sometimes admins forget that they **change the VPN settings and change a group that is allowed to access the VPN location**. If the user is not a part of that group which VPN location is configured to access:

<figure><img src="../../.gitbook/assets/Screenshot 2024-03-24 at 19.44.57.png" alt=""><figcaption></figcaption></figure>

will not be able to connect.

## I can access VPN but not my local network / Internet

Defguard only manages VPN server configuration (for now, we are planning ACLs / firewall management) which means it basically configures the VPN integrace and peers.

Then when the client / user connects it actually establishes **a secure tunel between their computer network and your server (that VPN interface)**.

From there, what happens to this traffic is the **administrator role.** The most common scenarious to do are:

* add routing rules, so that the traffic from that interface/VPN IP network gets routed to your network - this approach gives the advantage that users VPN ip persists in the network and the user is visible with it's VPN ip in your local network
* Masquerade or NAT - a common use case is to masquerade or NAT the traffic - which is **actually required if you want users to access Internet from the VPN -** this process is [described in detailed in this tutorial](../../tutorials/step-by-step-setting-up-a-vpn-server/#enabling-to-access-internet-through-your-vpn).
