---
icon: rotate-exclamation
---

# Automatic (real time) desktop client configuration & sync

When initially configuring defguard desktop client all available locations for the user (with all location settings) is automatically configured (which is one of defguard's uniqe functionalities).

In the course of time: new locations can be added by administrators, existing ones may change the configuration (DNS, network, etc.) or a user will be assigned to a new group (which for example doesn't have access to some locations any more).

In order to reconfigure users desktop client the administrator has two possibilities:

1. If using the **Open Source Open Core** - the administrator needs to send a new configuration token to each user affected, and the user needs to [Update the instance](../../help/configuring-vpn/add-new-instance/update-instance.md) in the desktop client with the new obtained token.
2. Obtain the **Enterprise License**, then each users desktop client (and all Locations) are **reconfigured automatically, real time** (propagation takes around 30 seconds to 1 minute) each time any VPN Location is reconfigured or the user is assigned to a different group.&#x20;

{% hint style="warning" %}
If you have been using defguard prior to version 1.0.0, upgraded and have Enterprise License, to take advantage of the real-time config sync on an already configured desktop client, [please refer to Upgrade notes documentation.](../../features/setting-up-your-instance/upgrading.md#desktop-client-real-time-sync)
{% endhint %}

