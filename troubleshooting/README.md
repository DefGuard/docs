---
description: Here are some common issues / problems that are frequently encountered.
icon: comment-question
---

# Troubleshooting Guide

{% hint style="info" %}
Before contacting support, please see if the answer cannot be found here:
{% endhint %}

## Desktop client real-time/auto sync doesn't work

The client communicates with core to initiate the handshake through the secure proxy (which is also the enrollment service - [more details about the architecture here](../admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/architecture.md)), so it's critical it works and is properly configured.

Common problems may be:

1. proxy / enrollment isn't working
2. DEFGUARD\_ENROLLMENT\_URL is set incorrectly in core - if set incorrectly the client will try to connect to default URL which is "http://localhost:8080" and MFA will not work.

## Nothing happens after clicking on "Use authenticator app" in client

See the "[Desktop client real-time/auto sync doesn't work](./#desktop-client-real-time-auto-sync-doesnt-work)" answer.

## I use Multi-Factor Authentication and am disconnected after _X-time_

After enabling Multi-Factor Authentication for a location the configuration of the gateway changes. Without MFA peers (devices) are persistent (always in the Kernel memory) and gateway only changed the gateway configuration if a peer is added/removed/changed.

But when MFA is enabled peers **are only added to the gateway** after successful MFA on the client (and pre-shared key exchange with client and gateway to establish a dedicated key for the session).

Also there is a setting in the location named _Peer Disconnect Threshold (seconds):_

<figure><img src="../.gitbook/assets/Screenshot 2024-11-15 at 18.25.33.png" alt="" width="375"><figcaption></figcaption></figure>

This setting specifies that if the **peer is inactive for&#x20;**_**(defined seconds)**_, the gateway **should remove it from the configuration** (as it should not be persistent since MFA is required).

So if you are disconnected from the location:

1. Check what is your setting for peer disconnect.
2. We have a known bug - that after disconnecting Defguard Desktop Client doesn't properly detect that and has a still active connection (and actually is disconnected). You need to reconnect. Also it's [being fixed and will be released in 1.1 version of the client](https://github.com/DefGuard/client/issues/351).

## Client: failed to configure DNS (Linux)

This error commonly occurs on Ubuntu 22. Defguard client internally calls `resolvconf` to set DNS servers. The only tested backend is `systemd-resolved`, so make sure you use it before proceeding further (`systemctl status systemd-resolved`).

On newer Ubuntu distributions (23 and up) `resolvconf` is, by default, a symbolic link to `resolvectl` and this is the recommended way of interacting with the system's DNS configuration. On Ubuntu 22 the symbolic link doesn't exist and the most straightforward way to fix this issue is to manually create it:

```bash
sudo ln -s /usr/bin/resolvectl /usr/sbin/resolvconf
```

If this fails, one may also try installing one of the packages providing the `resolvconf` command, like `openresolv` or `resolvconf` but this has not been tested and may possibly cause issues with `systemd-resolved`, so proceed at your own risk.

## TOTP / Email codes for MFA do not work

If you are having problems with TOTP codes form 2FA/MFA (when logging in to defguard or when connecting to VPN) please make sure your clock on the server that defguard core is running is set properly.

Best would be to setup on the server NTP time synchronization.

## I get the following error: There was a network error. Can't reach proxy

This error may happen when the client is unable to make a POST/GET request to your proxy web server. Make sure your proxy is accessible by the client, e.g. by making a GET request by hand or visiting your enrollment/proxy page from the same machine you are running the client on.&#x20;

In some rarer cases, your web server may not accept TLS versions lower than 1.3. Since the maximum version of TLS supported by the client is 1.2, it won't be able to connect to your web server, producing the mentioned error.

## I get the following error: _h2 protocol error: http2 error: stream error received: not a result of an error_

This error is common if you use a reverse-proxy for any of our components.

Every reverse proxy has a timeout for keeping the connection alive. You can increase the timeout value to see less errors, but they will eventually appear.

Ignore them, this is a normal behaviour.

## How to check if the VPN is working / my VPN is not working

If you have configured your defguard instance correctly, after connecting to the VPN you should be able to ping your VPN server, for example if you have the following setup:

<figure><img src="../.gitbook/assets/Screenshot 2024-03-24 at 18.36.43.png" alt="" width="313"><figcaption><p>Example VPN server IP</p></figcaption></figure>

after connecting to VPN you should be able to ping: 10.1.1.1.

### VPN Location settings changed

Check if the VPN location configuration has changed. If it did and you do not have Enterprise license where real-time config sync is automatic, the user needs to [update their client configuration by updating that instance manually.](../help/configuring-vpn/add-new-instance/update-instance.md)

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

Another common problem is that **your server on which the gateway is working, has some firewall rules that interfere with VPN network.** Please examine carefully `ufw` and `iptables` (even if ufw is disabled there may be `iptables` rules).

### VPN location ACLs

If a user has no access to the VPN location, sometimes admins forget that they **change the VPN settings and change a group that is allowed to access the VPN location**. If the user is not a part of that group which VPN location is configured to access:

<figure><img src="../.gitbook/assets/Screenshot 2024-03-24 at 19.44.57.png" alt=""><figcaption></figcaption></figure>

will not be able to connect.

## I can access VPN but not my local network / Internet

Defguard only manages VPN server configuration (for now, we are planning ACLs / firewall management) which means it basically configures the VPN interface and peers.

Then when the client / user connects it actually establishes **a secure tunnel between their computer network and your server (that VPN interface)**.

From there, what happens to this traffic is the **administrator role.** The most common scenarios to do are:

* add routing rules, so that the traffic from that interface/VPN IP network gets routed to your network - this approach gives the advantage that users VPN ip persists in the network and the user is visible with it's VPN ip in your local network
* Masquerade or NAT - a common use case is to masquerade or NAT the traffic - which is **actually required if you want users to access Internet from the VPN -** this process is [described in detailed in this tutorial](../tutorials/step-by-step-setting-up-a-vpn-server/#enabling-to-access-internet-through-your-vpn).
