# Automatic (real time) desktop client configuration & sync

{% hint style="warning" %}
**Availability**

This feature is available in Business and Enterprise plans. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

When initially configuring Defguard desktop client, all available locations for the user (with all location settings) are automatically configured (which is one of Defguard's unique functionalities).

In the course of time: new locations can be added by administrators, existing ones may change the configuration (DNS, network, etc.) or a user will be assigned to a new group (which for example doesn't have access to some locations any more).

In order to reconfigure a user's desktop client, the administrator has two possibilities:

1. If using the **Open Source Open Core** - the administrator needs to send a new configuration token to each user affected, and the user needs to [update the instance](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#updating-instance) in the desktop client with the new obtained token.
2. Obtain a **Business or Enterprise** license, then each user desktop client (and all Locations) are **reconfigured automatically in real time** (propagation takes around 30 seconds to 1 minute) whenever any VPN Location is reconfigured or the user is assigned to a different group.

{% hint style="warning" %}
If you have been using Defguard prior to version 1.0.0, upgraded and have Enterprise License, to take advantage of the real-time config sync on an already configured desktop client, [please refer to Upgrade notes documentation.](../../deployment-strategies/upgrading.md#desktop-client-real-time-sync)
{% endhint %}
