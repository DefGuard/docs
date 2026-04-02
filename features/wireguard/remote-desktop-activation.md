---
description: How to manually generate token for user as an administrator.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/wireguard/remote-desktop-activation
---

# Remote desktop client configuration

This process enables system **administrators** to create and distribute desktop **activation tokens to users facing access issues to the Defguard instance**. It's handy if a user is already enrolled (has an account) but has not configured the desktop client and doesn't have access to Defguard (is outside the internal network and can't access Defguard).

{% hint style="info" %}
Users can activate / configure their desktop client themselves - for that documentation please go to: [Adding an instance in the client documentation](../../using-defguard-for-end-users/desktop-client/instance-configuration.md).
{% endhint %}

Navigate to the user's list page.

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

Select "Configure Desktop Client" from the action menu.

{% hint style="info" %}
This option is only available if the instance has at least one localization, and the user is 'active'. For users that require enrollment, you can choose the option 'Start enrollment' and that token will also work with the client.
{% endhint %}

<figure><img src="../../.gitbook/assets/Remote Desktop Client config.png" alt=""><figcaption></figcaption></figure>

You will be presented with a choice to send an activation token via email or you can choose to just display the token and deliver it through other methods.

<figure><img src="../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

After receiving the token, the user will need to follow the activation process in the client. You can find out more about it in [#adding-instance](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#adding-instance "mention").

This token also allows for updating information, read more about it in [#updating-instance](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#updating-instance "mention").

Here is a video describing whole process:

{% embed url="https://www.youtube.com/embed/KdyZoAlBb9k" %}
