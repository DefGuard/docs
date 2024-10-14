---
description: How to manually generate token for user as an administrator.
---

# Remote desktop client configuration

This process enables system **administrators** to create and distribute desktop **activation tokens to users facing access issues to the defguard instance**. It's handy if a user is already enrolled (has an account) but has not configured the desktop client and doesn't have access to defguard (is outside the internal network and can't access defguard).

{% hint style="info" %}
Users can activate / configure their desktop client themselves - for that documentation please go to: [Adding an instance in the client documentation](../../help/configuring-vpn/add-new-instance/).&#x20;
{% endhint %}

Navigate to the user's list page.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Select "Configure Desktop Client" from the action menu.

{% hint style="info" %}
This option is only available if the instance has at least one localization, and the user is 'active'. For users that require enrollment, you can choose the option 'Start enrollment' and that token will also work with the client.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/Remote Desktop Client config.png" alt=""><figcaption></figcaption></figure>

You will be presented with a choice to send an activation token via email or you can choose to just display the token and deliver it through other methods.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

After receiving the token, the user will need to follow the activation process in the client. You can find out more about it in [add-new-instance](../../help/configuring-vpn/add-new-instance/ "mention").

This token also allows for updating information, read more about it in [update-instance.md](../../help/configuring-vpn/add-new-instance/update-instance.md "mention").
