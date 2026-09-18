# Configuring MFA for a location

{% hint style="warning" %}
MFA flows are new in 2.2, which is [currently in alpha](../../../deployment-strategies/pre-production-and-development-releases.md). Screens and labels may still change before release. Use a dedicated test user and a dedicated VPN location while testing, and keep a separate administrator account that does not depend on the location you are changing.
{% endhint %}

MFA is enforced per VPN location. A location does not have to use a single flow: it always has one **Default MFA flow**, and it can additionally have **group-level MFA overrides**, each assigning a different flow to selected groups. Every user connecting to the location resolves to exactly one of those flows.

This page assumes the flows already exist. If none do, create one first as described in [MFA flows and methods](mfa-flows-and-methods.md); MFA cannot be enforced on a location while no flow exists.

### Enforce MFA on the location

1. Open **Locations management** and edit the location you want to protect.
2. In the **Multi-Factor Authentication** section, turn on **Enforce Multi-Factor Authentication for this location**.
3. Pick the location's **Default MFA flow** in the modal that opens.
4. Set the **Client disconnect threshold (seconds)**. The lowest accepted value is 120; we recommend at least 300 (5 minutes). See [below](configuring-mfa-for-a-location.md#client-disconnect-threshold).
5. Optionally add [group-level overrides](configuring-mfa-for-a-location.md#assigning-more-than-one-flow-to-a-location).
6. **Save changes**.

<figure><img src="../../../.gitbook/assets/image (373).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
MFA and [service locations](../../service-locations.md) are mutually exclusive. A service location cannot enforce MFA, and enforcing MFA on a location turns its service location mode off.
{% endhint %}

### Assigning more than one flow to a location

Use a group-level MFA override when part of your users should prove something different from everyone else, for example when contractors have to add a hardware key step that staff do not.

1. In the location's **Multi-Factor Authentication** section, choose **Add group-level MFA**.
2. Pick the flow this override assigns, and the groups it applies to. At least one group is required.
3. Repeat for each further group of users that needs its own flow.
4. Drag the override rows to set their priority.
5. **Save changes**.

Each assignment on a location uses a flow of its own, so a location with a default and two overrides has three different flows assigned to it. A flow already used by one assignment is not offered to the others.

#### How a user's flow is resolved

Overrides are evaluated from the top of the list downwards. The first override whose groups include the connecting user decides their flow, and a user matching no override falls through to the default. Group membership therefore does not have to be exclusive: priority breaks the tie.

For example, with the overrides in this order:

1. **Contractors** to a flow requiring an authenticator code and a hardware key,
2. **Admins** to a flow requiring an authenticator code and a mobile biometric confirmation,

and the default flow requiring an authenticator code only, a user who is in both Contractors and Admins connects with the Contractors flow, because it is higher in the list. A user in neither group connects with the default flow.

This is also why the default can never be removed while MFA is enforced: it is the only assignment guaranteed to match every user.

{% hint style="info" %}
Group-level MFA overrides require an Enterprise license, and more than one flow on the instance requires at least a Business license. See [Purchasing and using the license](../../../enterprise/license.md).
{% endhint %}

### Client disconnect threshold

When MFA is enforced on a location, Defguard periodically (currently every **1 minute**) checks statistics to see whether a client is still active, and disconnects it once the inactivity period set in **Client disconnect threshold (seconds)** is reached. A disconnected client has to complete the MFA flow again to reconnect.

The gateway therefore needs to send [statistics](../../../in-depth/architecture/how-do-vpn-statistics-work.md) within that period. We recommend:

* the gateway sends statistics every 30 seconds;
* the Client disconnect threshold is at least 300 seconds (5 minutes).

### Client update after changing MFA

{% hint style="warning" %}
When MFA configuration is changed, all clients must do an [Instance Update](../../../using-defguard-for-end-users/desktop-client/instance-configuration.md#updating-instance). Until a client updates, it does not know about the new flow.
{% endhint %}

A location whose flow has more than one step requires **Defguard client 2.2.0 or newer** on every platform. Older clients can still connect to a flow that has a pre-2.2 equivalent, and a location they cannot represent is withheld from them rather than offered without MFA. See [Client compatibility](mfa-flows-and-methods.md#client-compatibility) for which shapes still work.

### When a method cannot be used

Defguard checks the method a client submits again when the connection starts, and rejects it without establishing a tunnel when the method:

* is not one of the methods in that step;
* is not covered by the instance [license](https://app.gitbook.com/s/pE5yODhpiy5pT1XxY0BS/enterprise), for example **External ID Provider** without a Business license;
* is not configured for that user or device, for example an authenticator app the user never enabled, **Email Verification Code** with no working SMTP, or **Biometrics** on a device with no biometric registration.

The user then has to complete the missing setup or pick another method from the same step. An unavailable method never shortens or skips a flow.
