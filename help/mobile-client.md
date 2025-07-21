# Mobile Client

{% hint style="warning" %}
Mobile client is currently under development **(closed beta)**, current page should be considered as preview. Some parts of UI may look different in upcoming release.
{% endhint %}

### Overview

This guide explains how to use the Defguard Mobile to connect securely to VPN locations managed within your Defguard [instance](mobile-client.md#what-is-an-instance). It covers the entire process, from installation, adding new instances, connecting to locations, to managing your VPN connection settings.

## Installation process

### Step 1: Download app 
1. Join closed beta for [iOS](https://defguard.net) or [Android](https://defguard.net). (Coming soon!)
2. Download and install the app on your device.

### Step 2: Add new Instance
{% hint style="info" %}
An instance is a container for VPN locations. Each instance can have multiple locations that you can connect to with a single click. 
{% endhint %}

1. Log in to your Defguard account.
2. Go to **"My Profile"** tab.
3. Click **"Add new device"** button inside **"User Devices"** list.
<figure><img src="../.gitbook/assets/defguard-myprofile-add-device.png" alt="" width="50%"><figcaption></figcaption></figure>

4. Select "Remote Device Activation" and click "Next". 
<figure><img src="../.gitbook/assets/defguard-add-new-device.png" alt="" width="50%"><figcaption></figcaption></figure>

5. After that you should see URL, Token and QR Code. **Take screenshot**, we will need it next step.
<figure><img src="../.gitbook/assets/defguard-add-instance-qr.png" alt="" width="50%"><figcaption></figcaption></figure>

6. Open Defguard Mobile on your smartphone
7. Click "Scan QR Code"
8. Scan QR generated in step 5.
9. Enter name of your device. For example "iPhone 11"
10. Confirm

{% hint style="info" %}
If you can't scan QR Code, select "Add Instance Manually" in **step 4** and enter URL and Token from **step 5**.
{% endhint %}

{% hint style="warning" %}
Your phone will need to add new VPN configuration, when you see the request, please allow it. Without this permission, Defguard cannot establish VPN connection.
{% endhint %}

### Step 3: Connecting to instance
 
{% hint style="info" %}
Some VPN locations require extra security when connecting. This is called MFA (Multi-Factor Authentication). There are two types:

* Internal MFA: You confirm your identity directly in the app, for example by entering a code from your Authenticator App or email.
* External MFA: You are redirected to a secure login page (like Google or Microsoft) outside the app to confirm your identity.
If a location uses MFA, you will see clear instructions in the app. Just follow the steps shown on your screen to connect safely.
{% endhint %}

1. Open Defguard
2. Click on Instance you want to connect to.
<figure><img src="../.gitbook/assets/defguard-instance-list.jpg" alt="" width="375"><figcaption></figcaption></figure>

3. Click **"Connect"** next to location you want to use.
<figure><img src="../.gitbook/assets/defguard-location-list.jpg" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
The first time you connect, app will ask whether you want to route **predefined traffic** or **all traffic**.

* **Predefined traffic** is optimized for general browsing and will route only specific traffic through VPN.
* **All traffic** provides full encryption and privacy for all your device traffic.

You can select **"Remember my choice"** if you don't want to be asked again.

To change your traffic routing method after your first connection:

1. Open the Defguard.
2. Go to **Instances** in the app menu.
3. Press the **Connect** button next to the location you want to update.
4. When the popup appears, look for the option **Select Traffic Routing**.
<figure><img src="../.gitbook/assets/defguard-mobile-connect-traffic.png" alt="" width="375"><figcaption></figcaption></figure>

5. Choose your preferred routing method and confirm with the **Save** button.


{% endhint %}

* Insert screenshot here

4. Choose your routing method
5. Confirm

{% hint style="warning" %}
If your location doesn't require MFA skip to step 8.
{% endhint %}
{% hint style="info" %}
When connecting with MFA for the first time, you will have the option to select **"Remember my choice"**. Select this option if you want to always use this method for this location.

To change your preferred MFA method later:

1. Go to **Instances** in the app menu.
2. Press **Connect** button next to location name and wait until popup shows.

<figure><img src="../.gitbook/assets/defguard-mobile-connect-mfa.png" alt="" width="375"><figcaption></figcaption></figure>

3. Click **Select MFA Method**, choose option and confirm with **Save** button.
{% endhint %}
4. Enter code from your [Authenticator App](mobile-client.md#authenticating-with-authenticator-app-totp) or Email.
{% hint style="warning" %}
If the VPN location requires OpenID for authentication, you will need to authorize in a different way. Instead of entering a code in the app, you will be redirected to a secure login page (for example, Google or Microsoft). Follow the instructions on the screen to log in. After successful authentication, after returning to Defguard, your connection will be established.
For detailed explanation of the OpenID authentication process, see the section "[Authenticating with OpenID](#authenticating-with-openid)".
{% endhint %}
5. Connect


#### Disconnecting from VPN

To disconnect from a VPN location in Defguard:

1. Open  Defguard.
2. Go to the active instance
3. Click **Disconnect** button next to the location you are currently connected to.

{% hint style="info" %}
After disconnecting, your device will stop sending traffic through the VPN and return to your regular internet connection. You can reconnect at any time by following the connection steps above.
{% endhint %}

## Additional information

### What is an instance?

An instance contains locations. These are the servers you can connect to via VPN. When you use the Defguard Mobile, you select an instance first, and then choose the specific location within it to establish your VPN connection.


### Authenticating with OpenID

Upon connecting location with **external** MFA requirement we should see this information

<figure><img src="../.gitbook/assets/defguard-mobile-openid-banner.png" alt="" width="375"><figcaption></figcaption></figure>

After clicking "Authenticate with OpenID", you should be redirected to login page. After successfully logging in, you should see something like this:

<figure><img src="../.gitbook/assets/defguard-mobile-openid-success.png" alt="" width="375"><figcaption></figcaption></figure>

After this step please go back to the Defguard and check your connection.

### Authenticating with Authenticator App (TOTP)

When connecting to a location that requires **internal** MFA, you should see this screen.

<figure><img src="../.gitbook/assets/defguard-mobile-totp-screen.png" alt="" width="375"><figcaption></figcaption></figure>

1. Open your Authenticator App (such as Google Authenticator or Microsoft Authenticator) on your phone.
2. Find the code generated for your Defguard account.
3. Enter this code into the Defguard.
4. Tap **"Verify"**.
