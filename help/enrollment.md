# Enrollment & Onboarding

Defguard provides **secure remote enrollment process (remote registration/activation)**, during which the user can:

* double-check their data
* setup their password
* add their initial device to access VPN as a nice wizard!
* see admin (that has added this user) **contact details**

All done by a nice wizard:

<figure><img src="https://github.com/DefGuard/docs/raw/docs/releases/0.7/enrollment.png?raw=true" alt=""><figcaption></figcaption></figure>

This can be done in **two ways** - either in the **browser** (web based) or by using a **defguard desktop client**. For both ways, there is an email that is sent to the user with explanation and relevant URLs/tokens.

{% hint style="warning" %}
When starting the enrollment process - a user will have only **10minutes** to complete the process.

The **enrollment token is valid for 24 hours.**
{% endhint %}

### Enrollment using desktop client

In the desktop client **when adding an instance** - the enrollment & onboarding process takes place.

What is great about this is that after the enrollment process is done - the desktop client is **automatically configured with all available VPN locations for the user.**

### **Web/browser based enrollment**

When accessing the enrollment service in the browser the process is extended with the possibility to configure the **initial VPN device/access manually.**

{% hint style="info" %}
Even if the user will do enrollment in the browser - the desktop client can be configured later by using: [Remote desktop client activation.](../admin-and-features/wireguard/remote-desktop-activation.md)
{% endhint %}

## User onboarding after enrollment

After the enrollment proces new users are provided with **any relevant company information, links to company systems, security guidelines**, etc.

The onboarding messages will be shown on the **last step of the enrollment process and sent to the user via email**:

<figure><img src="https://github.com/DefGuard/docs/raw/docs/releases/0.7/enrollment_msg.png?raw=true" alt=""><figcaption><p>Onboarding process</p></figcaption></figure>
