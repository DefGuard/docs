# Mobile Client

{% hint style="warning" %}
Mobile client is currently under development **(closed beta)**, current page should be considered as preview. Some parts of UI may look different in upcoming release.
{% endhint %}

### Overview

This guide explains how to use the Defguard Mobile to connect securely to VPN locations managed within your Defguard [instance](mobile-client.md#what-is-an-instance). It covers the entire process, from installation, adding new instances, connecting to locations, to managing your VPN connection settings.

### Installation

Join closed beta for [iOS](https://defguard.net/download) or [Android](https://defguard.net) (Coming soon!)

### Connecting made effortless 
1. Download app from [App Store](https://defguard.net/download) or [Google Play](https://defguard.net/download)
2. Open app
3. To add instance [scan QR](#add-instance-by-qr-code) or [use manual configuration](#add-instance-manually)
4. [Connect](#connecting-to-location-without-mfa) with single click


### Quick guide
- [How to add an instance?](#adding-new-instance)
- [How to connect to location without MFA?](#connecting-to-location-without-mfa)
- [How to connect to location with MFA?](#connecting-to-location-with-mfa)
- [How can I obtain QR Code?](#how-can-i-obtain-qr-code-url-and-token)
- [What is an instance?](#what-is-an-instance)
- [Supported MFA methods](#supported-mfa-methods)
- [Additional features](#additional-features)

### Adding new instance

<figure><img src="../.gitbook/assets/defguard-mobile-new-instance.jpg" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
In this stage you will need [QR code or URL with token](#how-can-i-obtain-qr-code-url-and-token) 
{% endhint %}

#### Add instance by QR Code

1. Go to "Add Instance" tab
2. Click "Scan QR Code"
3. Scan QR code
4. Enter name

#### Add instance manually

1. Go to "Add Instance" tab
2. Click "Add Instance Manually"
3. Enter URL and token
4. Confirm

{% hint style="warning" %}
Your phone will need to add new VPN configuration, when you see the request, please allow it. Without this permission, Defguard cannot establish VPN connection.
{% endhint %}

### Connecting to location (without MFA)

1. Choose the desired instance from list
2. Select the location by clicking "Connect" next to it
3. Choose if you want to route your traffic with:
   * Predefined traffic
   * All traffic
4. Press "Connect"

### Connecting to location (with MFA)

{% hint style="warning" %}
Please check [this](#authenticating-with-openid) section for information about how the OpenID authentication method works. 
{% endhint %}

1. Choose desired instance from list
2. Select location by clicking "Connect" next to it
3. Choose if you want to route your traffic with:
   * Predefined traffic
   * All traffic
4. Choose MFA method which is configured in your account
   * Email
   * [Authenticator App](#authenticating-with-authenticator-app-totp)
5. Authenticate and connect


{% hint style="info" %}
The first time you connect, app will ask whether you want to route **predefined traffic** or **all traffic**. 

- **Predefined traffic** is optimized for general browsing and will route only specific traffic through VPN.
- **All traffic** provides full encryption and privacy for all your device traffic.

You can select **"Remember my choice"** if you don't want to be asked again. 


If you want to change this setting later:
1. Go to **Instances** in the app menu.
2. Press **Connect** button next to location name and wait until popup shows.
3. Click **Select Traffic Routing**, choose option and confirm with **Save** button.

{% endhint %}

<figure><img src="../.gitbook/assets/defguard-mobile-connect-traffic.png"width="50%"><figcaption></figcaption></figure>

{% hint style="info" %}
When connecting with MFA for the first time, you will have the option to select **"Remember my choice"**. Select this option if you want to always use this method for this location.

To change your preferred MFA method later:
1. Go to **Instances** in the app menu.
2. Press **Connect** button next to location name and wait until popup shows.
3. Click **Select MFA Method**, choose option and confirm with **Save** button.

{% endhint %}
<figure><img src="../.gitbook/assets/defguard-mobile-connect-mfa.png"width="50%"><figcaption></figcaption></figure>

### Authenticating with OpenID
Upon connecting location with **external** MFA requirement we should see this information
<figure><img src="../.gitbook/assets/defguard-mobile-openid-banner.png"width="50%"><figcaption></figcaption></figure>

After clicking "Authenticate with OpenID", we should be redirected to login page. After successfully logging in, you should see something like this:
<figure><img src="../.gitbook/assets/defguard-mobile-openid-success.png"width="50%"><figcaption></figcaption></figure>
After this step please go back to the Defguard app and check your connection.

### Authenticating with Authenticator App (TOTP)

When connecting to a location that requires **internal** MFA, you should see this screen.
<figure><img src="../.gitbook/assets/defguard-mobile-totp-screen.png"width="50%"><figcaption></figcaption></figure>

Enter code from **your Authenticator App** and click "Verify". After this step your VPN connection will be established.


### How can I obtain QR Code, URL and token?

1. Login to your Defguard instance
2. Go to "My Profile" tab
3. Click "Add new device" in "User devices" tab

<figure><img src="../.gitbook/assets/defguard-myprofile-add-device.png" alt="" width="50%"><figcaption></figcaption></figure>

4\. Select "Remote Device Activation" and click "Next"

<figure><img src="../.gitbook/assets/defguard-add-new-device.png" alt="" width="50%"><figcaption></figcaption></figure>

5\. You will see URL, Authentication token and \*\*QR Code\*\*

<figure><img src="../.gitbook/assets/defguard-add-instance-qr.png" alt="" width="50%"><figcaption></figcaption></figure>

### What is an instance?

An instance contains locations. These are the servers you can connect to via VPN. When you use the Defguard Mobile, you select an instance first, and then choose the specific location within it to establish your VPN connection.

#### Instance list

<figure><img src="../.gitbook/assets/defguard-instance-list.jpg" alt="" width="375"><figcaption></figcaption></figure>

#### Location list (inside instance)

<figure><img src="../.gitbook/assets/defguard-location-list.jpg" alt="" width="375"><figcaption></figcaption></figure>

### Supported MFA Methods

Defguard Mobile supports multiple secure authentication methods:

* **TOTP** – time‑based one‑time passwords (Authenticator apps)
* **Email** – receive verification codes via email
* **OpenID** – integrate with any OpenID Connect provider (Google, etc.)

## Additional features

* Switch to **dark/light theme** — adapts to your system settings
* View **live connection stats**, logs & tunnel details
* Manage multiple instances
* Choose if you want to route all or predefined traffic
