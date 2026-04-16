---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/service-locations
---

# Service locations

{% hint style="warning" %}
#### Availability

This feature is available in Enterprise plan. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

{% hint style="warning" %}
Service locations are currently only supported with Defguard Client for Windows.
{% endhint %}

Service locations are a special kind of locations that allow establishing automatic VPN connections on system boot.

There are currently two modes of service locations:

* **Pre-logon**: the VPN connection to the location is established on system boot and is terminated when the user completes login to their system account. This may be used when your users need to authorize with some external identity provider (for example Active Directory) in order to successfully login and later don't require constant access to the VPN location.
*   **Always on**: the VPN connection to the location is established on system boot and is never terminated, unless:

    * The network configuration of this location is changed (connection is re-established, to apply new changes)
    * The client is reinstalled (connection is re-established during the reinstallation)
    * The service location mode is changed or is disabled (connection is terminated)

    This mode may be used when your users require constant access to a given VPN location.

## Creating service locations

1. Go to locations page, click "**Add location**"

<figure><img src="../.gitbook/assets/Screenshot 2026-04-16 at 14.00.44.png" alt=""><figcaption></figcaption></figure>

2. Select "**Service location (Windows only)**"

<figure><img src="../.gitbook/assets/Screenshot 2026-04-16 at 13.57.10.png" alt=""><figcaption></figcaption></figure>

3. The process of creating a location will be exactly the same up to the “Service Location Settings” step
4. During "Service Location Settings" step, select "[Pre-logon](service-locations.md#pre-logon)" or "[Always on](service-locations.md#always-on)" connection type

<figure><img src="../.gitbook/assets/Screenshot 2026-04-16 at 14.03.59.png" alt=""><figcaption></figcaption></figure>

Once you've completed the Wizard and adopted the Gateway, your service location will start working

## Managing service locations

After going to "**Locations**" page, you can see type of each location.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-16 at 14.06.32.png" alt=""><figcaption></figcaption></figure>



If you want to edit your service location, click "**…**" and select "**Edit"**

<figure><img src="../.gitbook/assets/Screenshot 2026-04-16 at 14.09.38.png" alt=""><figcaption></figcaption></figure>

Scroll down, you will see "**Location type (Windows only)**" section

<figure><img src="../.gitbook/assets/Screenshot 2026-04-16 at 14.10.35.png" alt=""><figcaption></figcaption></figure>

Here you can change service location mode, and confirm with "**Save changes**" button.

{% hint style="warning" %}
If your location is MFA protected, you won't be able to set is as a service location. The location must have MFA disabled in order to use service location mode.
{% endhint %}

After the configuration of the Defguard client is updated for your instance, the location will be hidden in the client's UI. The connection to the location will be established in the background without any user input.

## Network configuration updates

If you have enterprise features enabled, the Defguard Client periodically updates its network configuration if it's changed in Defguard Core. This also applies to service locations, but in order for the configuration update to happen for a service location, the Defguard Client must be open. This means that the configuration won't be updated when the user hasn't logged in yet, since the Client is not running at that point. In other words, **the user must first log in and start the Client for a configuration update to automatically happen**.

## Service location connection management in depth

{% hint style="info" %}
This section describes the current behavior of the Defguard Client on Windows.
{% endhint %}

Service locations are managed by a background service (`defguard-service`) responsible for managing VPN connections. The background service is running independently from the Desktop Client and is always active. The service is responsible for establishing the connection on system boot and terminating/restarting it under specific circumstances (e.g. when user logs in if using the pre-logon mode).

### Pre-logon

If you selected the pre-logon mode, the connection will be established on system boot. If the service detects a login event, the connection will be terminated.

After login, the connection won't be established unless a system logoff event is detected. In such case, the connection is established again until another login event occurs.

### Always-on

If you selected the always-on mode, the connection will be established on system boot.

The connection won't be terminated or restarted unless the Desktop Client receives a network update or is uninstalled.

## Troubleshooting

### Restarting the background service

In order to force a reconnect to the service location, you can restart the background service that manages it. To do this on Windows, go to search and open `services.msc` as an administrator.

<figure><img src="../.gitbook/assets/obraz (22).png" alt=""><figcaption></figcaption></figure>

Next, find a service called "Defguard Service". Right click on it and select the restart option.

<figure><img src="../.gitbook/assets/obraz (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/obraz (21).png" alt=""><figcaption></figcaption></figure>

This will restart the background service and re-connect to the service location.

### Checking if connection is established

This can be checked either by:

1. Going to the Defguard Core web interface and checking if the given Client is connected. This is a less reliable way as it can report the status with a delay. We recommend checking the status directly on the given machine.
2. On the given Windows machine:
   1.  Go to "Settings"

       <figure><img src="../.gitbook/assets/obraz (23).png" alt=""><figcaption></figcaption></figure>
   2.  Network & Internet

       <figure><img src="../.gitbook/assets/obraz (24).png" alt=""><figcaption></figcaption></figure>
   3.  Change adapter settings

       <figure><img src="../.gitbook/assets/obraz (25).png" alt=""><figcaption></figcaption></figure>
   4. The VPN connection should be visible here, if it's established (for example in the case of an always on mode).

### Network change

If the network you use to access the internet (or more specifically, the VPN gateway) changes (for example, you switch Wi-Fi's while the service location connection is established, the connection will stop working. To resolve this issue, you will need to restart your device or restart the service responsible for management of the service locations, so the connection is re-established.
