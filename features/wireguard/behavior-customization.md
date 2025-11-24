# VPN & Client behaviour customization

{% hint style="warning" %}
This is an enterprise feature. To use it, purchase our [enterprise license](../../enterprise/license.md) or ensure that your deployment does not exceed the [usage limits](../../enterprise/license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

After purchasing the Enterprise License the _Enterprise features_ **tab will be activated**, enabling the administrator to configure additional features:

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

### Disable for users to manage their devices

When this option is enabled, **only users in the Admin group can manage devices in user profile**, for any other users adding/editing/removing their VPN devices is disabled.

### Disable ability to configure other VPN clients then Defguard desktop client

If '_Disable users' ability to manually configure WireGuard client_' option is **enabled**, then any user **has only possibility to configure Defguard desktop client.**

This option will not be available for users:

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-14 at 12.44.15.png" alt=""><figcaption></figcaption></figure>

### Client traffic policy selection

One of the unique features of Defguard desktop client is the ability for users to choose whether to route only **predefined network traffic** or **all traffic** from their device through a connected VPN location.

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

However, in some cases administrators may want to enforce a specific behavior - allowing access only to predefined traffic or requiring all traffic to pass through the VPN.\
The **Client Traffic Policy** setting enables administrators to control this behavior as needed. The available options are:

* **None** - Users can freely choose between routing predefined traffic or all traffic through the VPN.
* **Disable all traffic** - Only predefined traffic is allowed, the "All traffic" option is disabled for users.
* **Force all traffic** - All traffic is routed through the VPN, the "All traffic" option is enforced and cannot be changed by users.

{% hint style="warning" %}
Please note that this option is only client-side enforced, meaning the user may manually modify Wireguard interface to force all traffic to go through the VPN.
{% endhint %}
