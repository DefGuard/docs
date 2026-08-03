# External SSO based MFA

In order to enable the External MFA authentication:

1. Your instance **must have** [external OIDC/SSO configured](../../external-openid-providers/).
2. Choose **External MFA** during location configuration or edit the desired location from **Locations** list.

<figure><img src="../../../.gitbook/assets/image (302).png" alt=""><figcaption></figcaption></figure>

#### Client disconnect threshold

When MFA is enabled on a location, Defguard periodically (currently every **1 minute**) checks statistics if a client is connected and if the period of inactivity (defined in this option) is met, a client is disconnected.

Thus, the gateway needs to be configured to send statistics in that period.

{% hint style="info" %}
We recommend to set:

* Gateway to send statistics every 30sec
* Client disconnect threshold we recommend it to be min. 300 (5 min)
{% endhint %}

### Testing MFA on Defguard client

When a location has External MFA enabled, after clicking Connect in the Desktop client ([here you can find information about Mobile Client External MFA](../../../using-defguard-for-end-users/mobile-client/instance-connect.md#external-mfa)), there will be information displayed about authentication requirement:

<figure><img src="../../../.gitbook/assets/auth-with-openid-modal.png" alt="" width="375"><figcaption></figcaption></figure>

In order to authenticate the user will be prompted to click on Authenticate with your configured OIDC (like Authenticate with Google) - which will open the browser and start the authentication session with your OIDC/SSO provider by the [Defguard Enrollment ](../../../using-defguard-for-end-users/enrollment/)service (which is the only public component).

After successful authentication, the user will be informed by the enrollment service like so:

<figure><img src="../../../.gitbook/assets/auth-completed-openid.png" alt="" width="375"><figcaption></figcaption></figure>

And the VPN connection will be established immediately.

Video describing whole process:

{% embed url="https://www.youtube.com/embed/81MH7VXmHR0" %}

## Biometry as an internal MFA method

Users can use biometry as an internal MFA method on their mobile devices. If a device has configured biometry as an MFA method, you will see fingerprint icon, next to the device name on devices list within user's profile.

<figure><img src="../../../.gitbook/assets/image (303).png" alt="" width="337"><figcaption></figcaption></figure>
