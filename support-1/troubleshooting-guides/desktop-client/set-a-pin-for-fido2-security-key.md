# Set a PIN for  FIDO2 security key

The desktop client needs a PIN on your security key to register the key and to connect with it. The PIN is stored on the key itself, not in Defguard. Defguard cannot see, change, or reset it, so you manage it on your own computer.

{% hint style="info" %}
**Use the software from your key's manufacturer when you can.** It supports every feature of your key and shows the exact PIN rules for your model. On Windows, the built-in Windows settings also work for any FIDO2 key.
{% endhint %}

### Before you start

* Plug in only the key that you want to manage. Unplug any other security keys.
* Close the Defguard desktop client and any browser windows that wait for a security key. Only one app at a time can talk to the key.
* A FIDO2 PIN has at least 4 characters. Some keys and some organisations require a longer PIN. It can contain letters, digits, and symbols, not only numbers.

### Set a PIN when you register the key on Windows

On Windows you usually do not need to set the PIN in advance. When you register the key and click **Register**, the Windows Security window checks the key. If the key has no PIN, Windows asks you to create one:

1. Enter a new PIN in **New PIN**.
2. Enter it again in **Confirm PIN** and click **OK**.
3. Touch the key when it blinks.

The registration then continues as usual.

{% hint style="info" %}
On macOS and Linux the client does not set a PIN for you. Registration fails with a message that contains `CTAP2_ERR_PIN_NOT_SET`. Set a PIN with one of the methods below, then register the key again.
{% endhint %}

### Manufacturer software

Most security key manufacturers offer free software to manage their keys. You use it to set, change, or reset the PIN, and often for other settings of the key too. This is the recommended way to manage the PIN on every platform.

The table below lists software for some popular keys. If your key is not listed, look for the software on the manufacturer's website or in the documentation that came with the key.

| Manufacturer                | Software                                                                      | Notes                                                                                                                                                |
| --------------------------- | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Yubico (YubiKey)            | [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/) | Set or change the PIN in the **Passkeys** section. Reset FIDO from the reset option in the app menu.                                                 |
| Feitian                     | Feitian FIDO2 Manager, or BioPass FIDO2 Manager for fingerprint keys          | Download from the Feitian support site.                                                                                                              |
| Token2                      | Token2 FIDO2 Manager (Windows), `fido2-manage` (macOS, Linux)                 | Download from the Token2 website.                                                                                                                    |
| Google (Titan Security Key) | No separate app                                                               | Follow the steps in [Google's Titan Security Key help](https://support.google.com/titansecuritykey/). On Windows, you can also use Windows settings. |
| Any other FIDO2 key         | Check the manufacturer's website                                              | On Windows, you can also use Windows settings.                                                                                                       |

{% hint style="warning" %}
On Windows, Yubico Authenticator and some other manufacturer apps can manage FIDO2 only when you run them as administrator. Right-click the app and choose **Run as administrator**.
{% endhint %}

### Windows

Windows 10 and 11 can manage the PIN of any FIDO2 key without extra software.

1. Open **Settings** > **Accounts** > **Sign-in options**.
2. Click **Security key**, then **Manage**.
3. Insert the key and touch it when it blinks.
4. Choose what to do:
   * To set a PIN on a key that has none, click **Add** under **Security Key PIN**.
   * To change the PIN, click **Change** under **Security Key PIN**, enter the old PIN, then the new one twice.
   * To reset the key, click **Reset** under **Reset Security Key**. Read Reset the key first.

### macOS and Linux

macOS and Linux have no built-in settings for security keys. Use the software from your key's manufacturer. If you cannot find it, check the manufacturer's website or ask your administrator.

### Reset the key

Reset is the only fix when you forgot the PIN or the key is locked. A key locks after 8 wrong PINs in a row. After 3 wrong PINs in a row, unplug the key and plug it in again before you try once more.

{% hint style="danger" %}
A reset deletes every FIDO2 credential on the key, including your Defguard registration and the passkeys for all other websites and services. It cannot be undone. On a YubiKey, other features such as OTP, PIV, and OpenPGP stay as they are.
{% endhint %}

Most keys accept a reset only within a few seconds after you plug them in. If the reset fails with a timeout or "not allowed" error, unplug the key, start the reset again, plug the key in when asked, and touch it right away.

After the reset:

1. Set a new PIN with one of the methods above.
2. Register the key again in the desktop client. The old registration no longer works.

Until you register the key again, connect with another MFA method through **Other methods**, or ask your administrator for help.
