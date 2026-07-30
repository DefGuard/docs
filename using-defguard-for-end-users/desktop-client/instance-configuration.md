---
description: >-
  In this guide, you will learn how to add, remove and update Instance in
  Defguard desktop client.
---

# Instance configuration

{% hint style="warning" %}
Defguard Desktop Client is required if you want to use Multi-Factor Authentication, as any other WireGuard client doesn't support this functionality.
{% endhint %}

### Obtaining URL and Token

{% hint style="info" %}
If you are looking for how to generate tokens for your users as an Administrator, look here:

[remote-desktop-activation.md](../../features/wireguard/remote-desktop-activation.md "mention")
{% endhint %}

1. Log in to your Defguard account.
2. Go to **My Profile** tab and select **Devices** tab.
<figure><img src="../../.gitbook/assets/defguard-myprofile-tab-devices-tab-marked.png" alt="" width="50%"></figure>


3. Click **Add new device** button.
<figure><img src="../../.gitbook/assets/defguard-devices-add-new.png" alt="" width="50%"><figcaption></figcaption></figure>

4. Click **Client Activation**.

<figure><img src="../../.gitbook/assets/client-activation.png" alt="" width="50%"><figcaption></figcaption></figure>

5. Afterwards, use **One-Click Configuration** or click **Show advanced configuration** and copy **URL** and **Token** manually.

<figure><img src="../../.gitbook/assets/client-activation-instance-configuration.png" alt="" width="375"><figcaption></figcaption></figure>

### Manually Adding Instance

1. If you don't have **Defguard** in full view mode, open **Defguard** from tray view, and click **Open Defguard**
<figure><img src="../../.gitbook/assets/tray-view-open-defguard-marked.png" alt="" width="50%"></figure>

2. Click **Add Instance**.

<figure><img src="../../.gitbook/assets/add-instance-full-view.png" alt=""></figure>

3. Enter URL, Token and Device name of your choice, then click **Add Instance**. (If you don't have URL/Token, check out [this section](instance-configuration.md#obtaining-url-and-token))

<figure><img src="../../.gitbook/assets/add-instance-credentials.png" alt=""></figure>

### One-click Desktop Configuration

You can skip the whole process of entering the token/URL by clicking "One-Click Desktop Configuration". Here is a video describing the whole process:

{% embed url="https://www.youtube.com/embed/q5-AXOPLgHc" %}

### Connecting to Instance

#### Desktop client in full view mode

1. Go to **Instances**

<figure><img src="../../.gitbook/assets/instance-view-marked.png" alt=""></figure>

2. Select the instance you want to use

<figure><img src="../../.gitbook/assets/selected-instance-marked.png" alt=""></figure>

3. Choose allowed traffic
{% hint style="info" %}
* **Predefined traffic only** will only route traffic specified by your administrator.
* **All traffic is allowed** will route everything through VPN tunnel.
{% endhint %}

<figure><img src="../../.gitbook/assets/allowed-traffic-marked.png" alt=""></figure>

4. Click **Connect VPN**

<figure><img src="../../.gitbook/assets/connect-vpn-full-view.png" alt=""></figure>

#### Desktop client in tray view mode

1. Click on instance list

<figure><img src="../../.gitbook/assets/instance-list-marked.png" alt=""></figure>

2. Select the instance you want to connect to

<figure><img src="../../.gitbook/assets/selected-instance-on-list-marked.png" alt=""></figure>

3. Choose allowed traffic
{% hint style="info" %}
* **Predefined traffic only** will only route traffic specified by your administrator.
* **All traffic is allowed** will route everything through VPN tunnel.
{% endhint %}

<figure><img src="../../.gitbook/assets/allowed-traffic-tray-view.png" alt=""></figure>

4. Click **Connect VPN**

<figure><img src="../../.gitbook/assets/connect-tray-view-marked.png" alt=""></figure>



### Disconnecting from Instance

Click **Disconnect** next to the location you are currently connected to.

<figure><img src="../../.gitbook/assets/disconnect-marked.png" alt=""><figcaption></figcaption></figure>

### Updating Instance

If you want to update your instance manually:

1. If you don't have **Defguard** in full view mode, open **Defguard** from tray view, and click **Open Defguard**
<figure><img src="../../.gitbook/assets/tray-view-open-defguard-marked.png" alt="" width="50%"></figure>

2. Go to your Instance and click **Instance settings**

<figure><img src="../../.gitbook/assets/client-instance-settings-marked.png" alt=""></figure>

3. Click **Update**

<figure><img src="../../.gitbook/assets/update-marked-instance-settings.png" alt=""></figure>

{% hint style="warning" %}
Only tokens issued from that specific instance will work.
{% endhint %}

4. Enter Token provided by your administrator, or generate it [on your own](instance-configuration.md#obtaining-url-and-token). Then click **Update**

<figure><img src="../../.gitbook/assets/token-update-instance.png" alt=""><figcaption></figcaption></figure>

Your Instance will update immediately.

### Why do instances need updates?

Defguard Desktop stores all information locally and doesn't communicate with Defguard outside the registration process. This means that the information about an instance is a snapshot of the moment you registered it in the desktop client, and you might want to update it, for example when locations are added or removed.

{% hint style="success" %}
If you have a Business or Enterprise plan, all desktop clients and all instances are [synchronized automatically and in real-time.](../../features/remote-user-enrollment/automatic-real-time-desktop-client-configuration.md)
{% endhint %}

### Removing Instance

1. If you don't have **Defguard** in full view mode, open **Defguard** from tray view, and click **Open Defguard**
<figure><img src="../../.gitbook/assets/tray-view-open-defguard-marked.png" alt="" width="50%"></figure>

2. Go to your Instance, click **Instance settings**

<figure><img src="../../.gitbook/assets/client-instance-settings-marked.png" alt=""></figure>

3. Click **Delete**

<figure><img src="../../.gitbook/assets/delete-instance-marked.png" alt=""></figure>

4. Confirm with **Delete instance** button

<figure><img src="../../.gitbook/assets/delete-instance-confirmation.png" alt=""></figure>


Your Instance will be removed immediately.
