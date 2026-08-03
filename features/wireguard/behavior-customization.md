---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/wireguard/behavior-customization
---

# Client behaviour customization

{% hint style="warning" %}
**Availability**

This feature is available in Business and Enterprise plans. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

Navigate to **Settings → General → Client behaviour.**

<figure><img src="../../.gitbook/assets/image (313).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (314).png" alt=""><figcaption></figcaption></figure>

### Client configuration Permissions

Options that affect ability to interact with device management for users.

#### Device management

This option determines whether users can create and manage their own devices.

If disabled, **only administrators** can manage devices in user profiles. Users will retain the devices they already have but will not be able to add or manage new ones.

If disabled from the beginning, users will only be able to configure a **single device** during the enrolment process.

{% hint style="warning" %}
Keep in mind that advanced users can still copy the data directory of their client application and use it on another device.
{% endhint %}

<figure><img src="../../.gitbook/assets/image (315).png" alt=""><figcaption></figcaption></figure>

#### WireGuard configuration

This option disables ability to create native WireGuard configurations for users. This way users can only enroll new devices via Defguard client applications.

### Client traffic rules

One of the unique features of **Defguard desktop client** is the ability for users to choose whether to route only **predefined network traffic** or **all traffic** from their device through a connected VPN location.

However, in some cases administrators may want to enforce a specific behaviour - allowing access only to predefined traffic or requiring all traffic to pass through the VPN.

<figure><img src="../../.gitbook/assets/image (316).png" alt="" width="563"><figcaption></figcaption></figure>

\
The **Client Traffic Policy** setting enables administrators to control this behaviour as needed. The available options are:

* **None** - Users can freely choose between routing predefined traffic or all traffic through the VPN.
* **Disable all traffic** - Only predefined traffic is allowed, the "All traffic" option is disabled for users.
* **Force all traffic** - All traffic is routed through the VPN, the "All traffic" option is enforced and cannot be changed by users.

{% hint style="warning" %}
Please note that this option is only client-side enforced, meaning the user may manually modify Wireguard interface to force all traffic to go through the VPN.
{% endhint %}
