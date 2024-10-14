# All enteprise features

Here is a list of all Enterprise features:

* [Ability to use external OIDC](external-openid-providers.md) (Google/Microsoft/Custom) to login or create defguard account.
* Ability to [automatically reconfigure users desktop client](automatic-real-time-desktop-client-configuration.md) (now requires manual action of updating the instance with a token).
* Ability to [disable for users to manage their devices](behavior-customization.md#disable-for-users-to-manage-their-devices) (just admin will have this possibility).
* Ability to [disable for users to configure WireGuard clients other then defguard desktop client](behavior-customization.md#disable-ability-to-configure-other-vpn-clients-then-defguard-desktop-client).
* Ability to [disable "All traffic" in the desktop client ](behavior-customization.md#disable-all-traffic-option-in-the-desktop-client)- just "predefined" traffic by admins.

#### In development

{% hint style="info" %}
Please contact sales \[ a t ] defguard.net if you would like to test those features
{% endhint %}

* Full encryption of desktop client data including wireguard keys, configurations etc - this option requires for users to enter a password or use YubiKey that was provisioned by defguard to open and decrypt the desktop client.
