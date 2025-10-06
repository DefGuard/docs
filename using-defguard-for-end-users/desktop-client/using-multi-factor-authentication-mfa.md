# Using Multi-Factor Authentication (MFA)

{% hint style="danger" %}
Connecting to location with required **external** MFA is possible in the desktop client since [**version 1.5.0**](https://app.gitbook.com/s/kHPDOBrb5X1TB8O3GsjW/using-defguard-for-end-users/desktop-client/using-multi-factor-authentication-mfa#external-mfa)**.**
{% endhint %}

## Internal MFA

1. Open Defguard client, select your Instance and click **Connect** next to location with required MFA

<figure><img src="../../.gitbook/assets/defguard-client-instances.png" alt=""><figcaption></figcaption></figure>

2. Choose method configured for your account, and click **Connect**.
   * If you're using "Email" method, please enter the code sent to your email.
   * If you're using "Authenticator App", please enter code generated within your authenticator app.

{% hint style="info" %}
If you don't know how to setup or use your **Authenticator App** please check [this article](../setting-up-2fa-mfa.md#setting-up-2famfa) for detailed information.
{% endhint %}

<figure><img src="../../.gitbook/assets/defguard-client-internal-mfa.png" alt="" width="563"><figcaption></figcaption></figure>

3. After entering code, click **Verify**

<figure><img src="../../.gitbook/assets/defguard-client-2fa.png" alt=""><figcaption></figcaption></figure>

Your connection will be established immediately after this step.
