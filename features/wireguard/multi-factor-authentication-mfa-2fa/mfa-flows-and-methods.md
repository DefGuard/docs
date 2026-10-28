# MFA flows and methods

{% hint style="warning" %}
MFA flows are new in 2.2, which is [currently in alpha](../../../deployment-strategies/pre-production-and-development-releases.md). Screens and labels may still change before release.
{% endhint %}

{% embed url="https://www.youtube.com/watch?v=oK8OILe8nbY" %}

An **MFA flow** is a named, ordered list of steps. Each step holds the factors that are allowed to satisfy it, the user proves one factor per step, and the VPN tunnel is established only after the last step succeeds.

Flows exist independently of locations. One flow can be reused by several locations, and a single location can have [several flows assigned to it](configuring-mfa-for-a-location.md). Build the flow first, then enforce it on a location.

### Create a flow

1. Go to **Identity & Access** and open **MFA Flow**. Existing flows are listed under **All flows** with their steps.
2. Choose **Add new MFA flow**.
3. Under **General settings**, give the flow a **Title**. Users see this title, so name it after what it requires rather than after the location.
4. Under **Multi-Factor Authentication Methods**, use **+ Add factor** to put factors into **Step 1**.
5. Choose **Add MFA step** for each further step, in the order the user will complete them, and add its factors the same way.
6. Choose **Create MFA flow**.

<figure><img src="../../../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Factors within one step are alternatives, not a checklist: the user picks one of them and proves it. Put a second factor into a step when some of your users may not have the first one, and use a further step when you genuinely want a second proof.
{% endhint %}

A step needs at least one factor, and a factor can appear only once per step.

### Available factors

| Factor                      | The user has to have                                                                                                                                                                      | Desktop client | Mobile client                |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------------------------- |
| **Authenticator App**       | [TOTP](../../../using-defguard-for-end-users/setting-up-2fa-mfa.md#one-time-password) enabled in their Defguard profile.                                                                  | Yes            | Yes                          |
| **Email Verification Code** | [Email MFA](../../../using-defguard-for-end-users/setting-up-2fa-mfa.md#email) enabled in their profile, and the instance needs working SMTP.                                             | Yes            | Yes                          |
| **External ID Provider**    | Account linked to the configured [OpenID provider](../../external-openid-providers/). Requires a Business license.                                                                        | Yes            | Yes                          |
| **FIDO2 Security Key**      | A certified hardware security key registered in the [desktop client](../../../using-defguard-for-end-users/desktop-client/using-a-security-key-fido2.md). Requires an Enterprise license. | Yes            | No                           |
| **Defguard Mobile Client**  | An enrolled mobile device with biometrics, which approves the desktop connection by scanning a QR code.                                                                                   | Yes            | Not as the connecting client |
| **Biometrics**              | An enrolled mobile device with biometrics. Marked **Mobile only** in the factor menu.                                                                                                     | No             | Yes                          |

{% hint style="info" %}
The **FIDO2 Security Key** factor accepts only keys that the user registered in the desktop client. At registration, Defguard checks that the key is a certified hardware security key. A passkey from the **Passkeys** card in the user's profile signs the user in to the web app, but it does not satisfy this factor. A user who has only such a passkey is blocked at this step until they register a key in the desktop client.
{% endhint %}

A factor can be part of a flow and still be unusable by a particular user or device. Defguard rechecks it when a connection starts and refuses a factor the user has not set up, so a step whose only factor some users lack will block them rather than let them through.

#### Why a factor is greyed out

The factor menu splits factors into **Available in your plan** and **Available in higher plans**, and says what is missing:

* _Upgrade your plan to Business to use this MFA method_ for **External ID Provider**;
* _Upgrade your plan to Enterprise to use this MFA method_ for **FIDO2 Security Key**;
* _Configure SMTP server in Settings (Notifications tab) to activate this MFA method_ for **Email Verification Code**;
* _Upgrade your plan to Business to add more than one MFA step_ on the button that adds a step.

Saving is refused for the same reasons, so configure [SMTP](../../notifications/setting-up-smtp-for-email-notifications.md) or an [OpenID provider](../../external-openid-providers/) before adding the factor that needs it.

### Licensing

| Configuration                                | Required license |
| -------------------------------------------- | ---------------- |
| One flow on the instance, with a single step | Open source      |
| More than one flow                           | Business         |
| More than one step in a flow                 | Business         |
| **External ID Provider** as a factor         | Business         |
| **FIDO2 Security Key** as a factor           | Enterprise       |
| Group-level MFA overrides on a location      | Enterprise       |

See [Purchasing and using the license](../../../enterprise/license.md).

### Change or delete a flow

Editing a flow changes it for every location it is assigned to. Afterwards, all clients have to do an [Instance Update ](../../../using-defguard-for-end-users/desktop-client/instance-configuration.md#updating-instance)before they can connect.

A flow in use cannot be deleted. Defguard names the locations that still depend on it and asks you to assign another flow, or to pick another default, first.

### Client compatibility

Multi-step flows need **Defguard client 2.2.0 or newer** on every platform, desktop and mobile alike. Older clients are not locked out of MFA, though: they can still use a flow whose shape has an equivalent in the pre-2.2 model, where a location was either Internal or External MFA.

| The location's MFA configuration                                                                                              | A pre-2.2 client                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| MFA not enforced                                                                                                              | Connects as usual                                                                                                            |
| One flow, one step, holding **Authenticator App**, **Email Verification Code**, **Biometrics** and **Defguard Mobile Client** | Connects, and behaves like the old Internal MFA. A **FIDO2 Security Key** factor in that step is ignored rather than offered |
| One flow, one step, holding **External ID Provider** alone                                                                    | Connects, and behaves like the old External MFA                                                                              |
| More than one step, more than one flow assigned, or a smaller set of factors in the single step                               | The location is not sent to the client at all                                                                                |

A location withheld this way simply does not appear in the client, which is deliberate: an old client is never offered a location whose requirements it cannot present, and never connects to it without MFA. Users on such a client have to upgrade before they can reach the location.

### Checking that a device can prove biometrics

A device with biometry configured as an MFA method shows a fingerprint icon next to the device name in the device list of the user's profile.

<figure><img src="../../../.gitbook/assets/image (372).png" alt=""><figcaption></figcaption></figure>

### Next step

Continue with [Configuring MFA for a location](configuring-mfa-for-a-location.md) to enforce a flow on a location and, where needed, give selected groups a different flow.
