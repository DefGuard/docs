# WebAuthn security keys

Currently, only security keys that can be configured as **passkeys** are supported.

**Passkeys** are a modern replacement for traditional passwords, based on WebAuthn and FIDO2 standards. They enable passwordless authentication by verifying both possession of a device and an additional user verification step (such as biometrics, PIN entry, or a user gesture).

For a security key to be supported, it must be able to correctly set the **User Verification (UV)** flag during the WebAuthn authentication flow. The UV flag indicates that the key has confirmed that the user is physically present and actively authenticating.

Some older or limited-functionality security keys do not support the necessary mechanisms (such as built-in biometrics or PIN prompts) to perform user verification. As a result, they **cannot set the UV flag** and are **not supported** for passkey use.

{% hint style="danger" %}
Attempting to register an **unsupported** security key will result in an **error** during **registration** process.
{% endhint %}

#### Examples of Unsupported Security Keys

* **YubiKey 4 Series** (e.g., YubiKey 4 Nano, YubiKey 4C)
* **YubiKey NEO**
* **Older FIDO U2F-only keys** without FIDO2/WebAuthn support

#### Requirements for Supported Keys

* Must support **passkey** registration and authentication (WebAuthn + FIDO2).
* Must be capable of **user verification** (e.g., PIN entry, biometrics, or touch gestures).

