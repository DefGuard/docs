# Configuring a device for new VPN Location manually

If you (or your Defguard administrator) have added a new VPN Location and you would like to connect to that location from your **existing device** (for which you have downloaded configuration for any previous locations), you need to:

1. Go to your account profile.
2. Open Devices tab.
3.  Click on device action button and choose **Show configuration**.<br>

    <figure><img src="../../.gitbook/assets/image (336).png" alt="" width="563"><figcaption></figcaption></figure>

The same configuration screen will popup as you were adding this device, but now you can choose a new location:

<figure><img src="../../.gitbook/assets/image (337).png" alt="" width="488"><figcaption></figcaption></figure>

4. **Download and configure your Wireguard VPN** exactly the same way you did it during the [adding device process](./).

The configuration will be named **LocationName-device.conf** - just add a new configuration to Wireguard client.

5. **Replacing the PrivateKey**

{% hint style="danger" %}
**Defguard doesn't store any user devices' private keys - you need to provide them**
{% endhint %}

Since Defguard doesn't have access to your private keys - **but has your public key stored for that device** - you need to replace the **PrivateKey value** in the new configuration location.

{% hint style="warning" %}
**Where to find the private key for that device?**

Yon can just **copy the whole line** from any other configurations from any other working locations for that device.
{% endhint %}
