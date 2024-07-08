---
description: Notes on the process of upgrading the Defguard client
---

# Upgrading

## 0.1.x -> 0.2.0

With this release we have added Multi-Factor Authentication  to the desktop client. Unfortunately desktop client database has change significantly as well as business logic (for example endpoints to proxy for MFA handshake). We have not stored them previously in the database - thus they cannot be recovered/updated automatically.

{% hint style="warning" %}
That unfortunately means you have to remove all your instances before upgrading (or just remove any desktop client configuration files, including the database) and start the enrollment (adding new instance) again after upgrading - just by adding a new device (you can remove the old one).
{% endhint %}
