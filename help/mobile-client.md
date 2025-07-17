# Mobile Client

{% hint style="warning" %}
Mobile client is currently under development, current page should be considered as preview. Some parts of UI may look diffrent in upcoming release.
{% endhint %}

### Installation

Download for [iOS](https://defguard.net) or [Android](https://defguard.net) (Coming soon!)

### Quick guide

* [How to add instance?](mobile-client.md#adding-new-instance-by-qr-or-manually)
* [How to connect to location without MFA?](mobile-client.md#connecting-to-location-without-mfa)
* [How to connect to location with MFA?](mobile-client.md#connecting-to-location-with-mfa)
* [Supported MFA methods](mobile-client.md#supported-mfa-methods)
* [Additional features](mobile-client.md#additional-features)

### Connecting made effortless

<figure><img src="../.gitbook/assets/defguard-mobile-connecting.gif" alt="" width="333"><figcaption></figcaption></figure>

### Adding new instance (by QR or manually)

<figure><img src="../.gitbook/assets/defguard-mobile-new-instance.jpg" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="warning" %}
Your phone will need to add new VPN configuration, when you see the request, please allow it. Without this permission, Defguard cannot establish VPN connection.
{% endhint %}

### Connecting to location (without MFA)

1. Choose desired instance from list
2. Select location by clicking "Connect" next to it
3. Choose if you want to route you traffic with:
   * Predefined traffic (Faster for general browsing)
   * All traffic (Full encryption and privacy)
4. Press "Connect"

### Connecting to location (with MFA)

1. Choose desired instance from list
2. Select location by clicking "Connect" next to it
3. Choose if you want to route you traffic with:
   * Predefined traffic (Faster for general browsing)
   * All traffic (Full encryption and privacy)
4. Choose MFA method which is configured in your account
   * Email
   * Authenticator App
   * Biometric
5. Authenticate and connect.

{% hint style="warning" %}
If your instance have configured external OpenID authentication, it will be used as primary method for MFA
{% endhint %}

### Supported MFA Methods

Defguard Mobile supports multiple secure authentication methods:

* **TOTP** – time‑based one‑time passwords (Authenticator apps)
* **Email** – receive verification codes via email
* **Biometry** – Touch ID / Face ID support
* **OpenID** – integrate with any OpenID Connect provider (Google, etc.)

## Additional features

* Switch to **dark/light theme** — adapts to your system settings
* View **live connection stats**, logs & tunnel details
* Manage multiple instances
* Choose if you want to route all or predefined traffic
