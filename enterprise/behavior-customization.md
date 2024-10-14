# VPN & Client behavior customization

After purchasing the Enterprise License the _Enterprise features_ **tab will be activated**, enabling the administrator to configure additional features:

<figure><img src="../.gitbook/assets/Enterprise Features.png" alt=""><figcaption><p>Additional Enterprise Features</p></figcaption></figure>

### Disable for users to manage their devices

When this option is enabled, **only users in the Admin group can manage devices in user profile**, for any other users adding/editing/removing their VPN devices is disabled.

### Disable ability to configure other VPN clients then defguard desktop client

If '_Disable users' ability to manually configure WireGuard client_' option is **enabled**, then any user **has only possibility to configure defguard desktop client.**

This option will not be available for users:

<figure><img src="../.gitbook/assets/Screenshot 2024-10-14 at 12.44.15.png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
Please note that defguard has only **desktop clients** and **no official mobile** client, thus when using this option users **will not be able to configure their mobile WireGuard clients.**
{% endhint %}

### Disable _All Traffic_ option in the desktop client

Of of defguard desktop client uniqe features is the possibility for the user to automatically route **All network traffic** from their device **through the connected VPN Location**, when the user checks _All traffic_ option_**:**_

&#x20;![](<../.gitbook/assets/Screenshot 2024-10-14 at 12.49.30.png>)

But there are scenarios that administrator would like that users have only access to the **Predefined traffic** (meaning Allowed IPs in the Network VPN configuration) and the possibility to access all networks disabled.

When enabling this option, users will only have _Predefined traffic_ available in their desktop client and the _All traffic_ option disabled.
