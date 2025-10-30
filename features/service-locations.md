# Service locations

{% hint style="warning" %}
Service locations are currently only supported with Defguard Client for Windows.
{% endhint %}

Service locations are a special kind of locations that allow establishing automatic VPN connections on system boot.

There are currently two modes of service locations:

* **Pre-logon**: the VPN connection to the location is established on system boot and is terminated when the user completes login to their system account. This may be used when your users need to authorize with some external identity provider (for example Active Directory) in order to successfully login and later don't require constant access to the VPN location.
*   **Always on**: the VPN connection to the location is established on system boot and is never terminated, unless:

    * The network configuration of this location is changed (connection is re-established, to apply new changes)
    * The client is reinstalled (connection is re-established during the reinstall)
    * The service location mode is changed or is disabled (connection is terminated)

    This mode may be used when your users require constant access to a given VPN location.

## Managing service locations

To make a location act as a service location, go to the given location's settings.

<figure><img src="../.gitbook/assets/Screenshot 2025-10-29 at 13.14.01.png" alt=""><figcaption></figcaption></figure>

Find the service locations section:

<figure><img src="../.gitbook/assets/obraz.png" alt=""><figcaption></figcaption></figure>

Here you can select one of the service location modes.

{% hint style="warning" %}
If your location is MFA protected, you won't be able to set is as a service location. The location must have MFA disabled in order to use service location mode.
{% endhint %}

After selecting one of the modes and saving your changes, the location will function as a service location. After the configuration of the Defguard client is updated for your instance, the location will be hidden in the client's UI. The connection to the location will be established in the background without any user input.

## Network configuration updates

If you have enterprise features enabled, the Defguard Client periodically updates it's network configuration if it's changed in Defguard Core. This also applies to service locations, but in order for the configuration update to happen for a service location, the Defguard Client must be open. This means that the configuration won't be updated when the user hasn't logged in yet, since the Client is not running at that point. In other words, **the user must first login and start the Client for a configuration update to automatically happen**.

## Service location connection management in depth

{% hint style="info" %}
This section describes the current behavior of the Defguard Client on Windows.
{% endhint %}

Service locations are managed by a background service (`defguard-service`) responsible for managing VPN connections. The background service is running independently from the Desktop Client and is always active. The service is responsible for establishing the connection on system boot and terminating/restarting it under specific circumstances (e.g. when user logs in if using the pre-logon mode).

### Pre-logon

If you selected the pre-logon mode, the connection will be established on system boot. If the service detects a login event, the connection will be terminated.&#x20;

After login, the connection won't be established unless a system logoff event is detected. In such case, the connection is established again until another login event occurs.

### Always-on

If you selected the always-on mode, the connection will be established on system boot.&#x20;

The connection won't be terminated or restarted unless the Desktop Client receives a network update or is uninstalled.&#x20;

## Troubleshooting

### Restarting the background service

In order to force a reconnect to the service location, you can restart the background service that manages it. To do this on Windows, go to search and open `services.msc` as an administrator.

Next, find a service called "Defguard Service". Right click on it and select the restart option.

This will restart the background service and re-connect to the service location.

### Network change

If the network you use to access the internet (or more specifically, the VPN gateway) changes (for example, you switch Wi-Fis) while the service location connection is established, the connection will stop working. To resolve this issue, you will need to restart your device or restart the service responsible for management of the service locations, so the connection is re-established.



&#x20;

