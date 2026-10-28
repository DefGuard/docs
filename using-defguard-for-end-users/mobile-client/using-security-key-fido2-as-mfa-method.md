# Using security key (FIDO2) as MFA method

A security key is a small physical device, like a YubiKey, that you tap against your phone to confirm it is really you. When a location asks for it, the Defguard mobile app lets you connect by entering the key's PIN and holding the key to your phone.

### What you need

* A supported security key (see below) with a PIN set.
* The key registered to your Defguard account from the **Defguard desktop client**.
* A phone with NFC. On Android, NFC has to be turned on.

{% hint style="info" %}
You cannot register a security key in the mobile app. Register it in the desktop client, and you can then use it on your phone too.
{% endhint %}

### Supported keys

Your key has to:

* **Support FIDO2.** Older keys that only support U2F will not work.
* **Support NFC.** The app reads the key wirelessly, so keys that only plug in over USB or Lightning cannot be used on the phone.
* **Be a certified hardware key.** Defguard accepts physical keys certified by the FIDO Alliance. Passkeys saved in a password manager, on your phone or in your computer cannot be registered.
* **Have a PIN set.** If your key has no PIN yet, set one using your key maker's app (for example Yubico Authenticator).

Common example is the YubiKey 5 NFC by Yubico. If you are not sure about your key, ask your administrator.

### Connecting with your security key

1. Open your instance and tap the location you want to connect to.
2. If the location lets you choose, select **FIDO2 Security key** and tap **Connect VPN**. In multi-step locations it appears as one of the steps.
3. Enter your key's PIN.
4. Tap **Verify now**.
5. Hold the key against your phone until verification finishes.

#### Where to hold the key

* **iPhone:** hold the key near the top edge of the phone.
* **Android:** hold the key against the back of the phone. The exact NFC spot depends on the model, usually in the middle or near the camera.

Keep the key still and in place until the app moves on. Pulling it away too early stops the verification.

### Troubleshooting

| What you see                                                                       | What to do                                                                                                                                                              |
| ---------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **FIDO2 Security key** shows "Not configured" or a "Security Key Required" message | Your account has no registered key yet. Register one in the Defguard desktop client.                                                                                    |
| **FIDO2 Security key** shows "Desktop only"                                        | Your Defguard server does not support security keys in the mobile app yet. Contact your administrator.                                                                  |
| "Turn on NFC to use your security key."                                            | Tap **Turn on NFC** and enable NFC in your phone's settings, then return to the app.                                                                                    |
| "This device has no NFC, so a security key cannot be used."                        | Your phone cannot read security keys. Use another method or the desktop client.                                                                                         |
| "No security key was detected."                                                    | Tap **Verify now** again and hold the key in the right spot. Remove a thick phone case if needed.                                                                       |
| "Keep the key against the phone until verification finishes."                      | The key moved away too soon. Try again and hold it still.                                                                                                               |
| "This security key has no PIN."                                                    | Set a PIN on the key with your key maker's app, then try again.                                                                                                         |
| "Incorrect PIN."                                                                   | Enter the correct PIN. The message shows how many attempts you have left.                                                                                               |
| "Too many wrong PINs. Move the key away, then tap it again."                       | Move the key away from the phone, bring it back and try again.                                                                                                          |
| "This security key's PIN is blocked."                                              | The key is locked after too many wrong PINs. Reset it with your key maker's app. Resetting erases the key, so you will need to register it again in the desktop client. |
| "This security key is not registered for your account."                            | You are using a different key from the one you registered. Use the registered key or register this one in the desktop client.                                           |
| "This key does not support FIDO2."                                                 | Use a key that supports FIDO2 over NFC.                                                                                                                                 |

{% hint style="warning" %}
Your key allows only a few wrong PIN attempts before it locks. If you are not sure about the PIN, stop and check it before trying again.
{% endhint %}
