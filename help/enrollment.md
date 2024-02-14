---
description: Desktop client can also enroll new users.
---

# Enrollment & Onboarding

Defguard provides **secure remote enrollment process**, during which the user can:

* double-check their data
* setup their password
* add their initial device to access VPN as a nice wizard!
* see admin (that has added this user) **contact data**

[![defguard enrollment](https://github.com/DefGuard/docs/raw/docs/releases/0.7/enrollment.png?raw=true)](../releases/0.7/enrollment.png)



### User onboarding after enrollment

Now you can easily share with new users **any relevant company information, links to company systems, security guidelines**, etc. In the enrollment module, you can write **custom messages** using [markdown](https://spec.commonmark.org/0.30/) that will be shown on the last step of the enrollment process and sent to the user via email:

[![defguard enrollment](https://github.com/DefGuard/docs/raw/docs/releases/0.7/enrollment\_msg.png?raw=true)](../releases/0.7/enrollment\_msg.png)

\
\
Desktop client also can enroll new users for an even more automated process that doesn't require them to set up their VPN manually.

{% hint style="warning" %}
This approach is only recommended when your Defguard instance has at least one location added. Otherwise, we advise you to use the [proxy ](https://github.com/DefGuard/proxy)frontend for enrolling new users.
{% endhint %}

For enrolling though the client user just needs to enter the enrollment token in [add-new-instance](configuring-vpn/add-new-instance/ "mention") process.

{% hint style="info" %}
You can learn more about enrollment in [remote-user-enrollment](../admin-and-features/remote-user-enrollment/ "mention").
{% endhint %}
