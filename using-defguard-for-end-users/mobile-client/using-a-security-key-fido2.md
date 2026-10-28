# Using a security key (FIDO2)

{% hint style="warning" %}
**Availability**

This feature requires an Enterprise plan. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

## Before you start

You need:

- Defguard mobile client 2.2.0 or newer on Android or iOS, on a phone with NFC. The mobile client reads the key only over NFC.
- A FIDO2 security key with NFC that Defguard trusts, for example a YubiKey 5 NFC or YubiKey 5C NFC. See [WebAuthn security keys](../../support-1/troubleshooting-guides/webauthn-security-keys.md).
- A PIN on the key. See [Set a PIN on the key](../desktop-client/using-a-security-key-fido2.md#set-a-pin-on-the-key).
- A location that accepts **Security key** as an MFA method. Your administrator sets this up in the location's [MFA flow](../../features/wireguard/multi-factor-authentication-mfa-2fa/mfa-flows-and-methods.md).

{% hint style="warning" %}
The mobile client cannot register a key. Register the key in the desktop client first, as described in [Register the key](../desktop-client/using-a-security-key-fido2.md#register-the-key). A passkey from the **Passkeys** section of your profile in the Defguard web app does not work.
{% endhint %}

## Connect with the key

1. Open your instance and tap the location. The **Connect** sheet opens.
2. In the MFA section, select **FIDO2 Security key** and tap **Connect VPN**. See [Connecting to Instance](instance-connect.md#connecting-to-a-location-with-mfa) for how the app picks the default method.
3. On the **Security key** screen, enter the key's PIN in **Security key PIN** and tap **Verify now**.
4. Hold the key to the phone until the verification finishes:
   - On an iPhone, hold the key near the top of the phone.
   - On Android, hold the key against the back of the phone.
5. If the location's MFA flow has more steps, complete them. When the last step succeeds, the app connects.

On Android, if NFC is off, the screen shows **Turn on NFC** instead of **Verify now**. Tap it, turn on NFC in the system settings, and go back to the app.

{% hint style="warning" %}
After 3 wrong PINs in a row, move the key away from the phone and hold it to the phone again before you try again. After 8 wrong PINs in a row, the key locks. The only way to unlock it is a reset, which deletes every credential on the key.
{% endhint %}

## Troubleshooting

| The app shows                                                  | What to do                                                                                                                    |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Security Key Required                                          | You have no registered key. [Register a key](../desktop-client/using-a-security-key-fido2.md#register-the-key) in the desktop client and try again. |
| This device has no NFC, so a security key cannot be used       | Use another MFA method, or connect from the desktop client.                                                                   |
| Turn on NFC to use your security key                           | Tap **Turn on NFC** and turn on NFC in the system settings.                                                                   |
| No security key was detected                                   | Tap **Verify now** again and hold the key to the phone.                                                                       |
| Keep the key against the phone until verification finishes     | Try again, and do not move the key until the verification finishes.                                                         |
| Enter your security key PIN, then tap the key                  | Enter the key's PIN.                                                                                                          |
| This security key has no PIN                                   | [Set a PIN](../desktop-client/using-a-security-key-fido2.md#set-a-pin-on-the-key) and try again.                              |
| Incorrect PIN                                                  | Check the PIN and try again. The message shows how many attempts are left.                                                    |
| Too many wrong PINs. Move the key away, then tap it again      | Move the key away from the phone, hold it to the phone again, and enter the PIN.                                              |
| This security key's PIN is blocked                             | The key needs a reset. Contact your administrator.                                                                            |
| This security key is not registered for your account           | Use the key that you registered, or [register this key](../desktop-client/using-a-security-key-fido2.md#register-the-key) in the desktop client. |
| Your security key was not accepted                             | Use a key that you registered in the desktop client, and enter its PIN.                                                       |
| This key does not support FIDO2                                | Use a key from [Before you start](using-a-security-key-fido2.md#before-you-start).                                            |

If you cannot connect at all, select another MFA method in the **Connect** sheet, or ask your administrator for help.
