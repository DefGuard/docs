---
description: Notes on upgrading Defguard and its components
---

# Upgrading

## Core & Proxy

### Core 0.8.x -> 0.9.x with Proxy 0.2.x -> 0.3.x

In this release we have **hardened the security architecture**, and since the Proxy component is open for HTTP commands and is frequently communitating with Core we have reversed the communication and now **Core is connecting to Proxy (Proxy is a GRPC server and Core is the client).**

This way if Core is in a secure network segment (like Intranet) and Proxy in a DMZ segment (where Internet traffic is allowed) you don't need to open on your firewall rules for Proxy from DMZ to connect to Intranet (no packet for New Connections from DMZ->Intranet).

This chage requires few changes if you are upgrading:

1.

## Desktop Client

### 0.1.x -> 0.2.0

With this release we have added Multi-Factor Authentication  to the desktop client. Unfortunately desktop client database has change significantly as well as business logic (for example enpoints to proxy for MFA handshake). We have not stored them previously in database - thus they cannot be recoverd/updated.

{% hint style="warning" %}
That unfortunately means you have to remove your instances (or just remove any desktop client configuratin files) and start the enrollement (adding new instance) again - just by adding a new device (you can remove the old one).
{% endhint %}
