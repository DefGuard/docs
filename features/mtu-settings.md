# MTU settings

Each VPN location has its own MTU (Maximum Transmission Unit) settings. You can set the MTU used by the gateway and the MTU sent to the users' clients, globally for the location or per group.

To change them, go to **Locations**, edit a location and find the **Maximum transfer unit (MTU) settings** section.

<figure><img src="../.gitbook/assets/image (378).png" alt=""><figcaption></figcaption></figure>

#### Gateway MTU

MTU of the WireGuard interface on the **gateways** serving this location.

#### Client MTU

This setting controls the MTU of the users [Defguard Desktop Client](../using-defguard-for-end-users/desktop-client/) for this location. There are two options:

* **Use the MTU value configured on the user's client** - Defguard doesn't send any MTU value, the client uses its own.
* **Assign custom MTU value for this location** - the value entered in **Default client MTU** is sent to all clients connecting to this location.

{% hint style="info" %}
A user can still override this value in the client. The MTU setting set in the client always takes precedence over the value sent by Defguard.
{% endhint %}

#### Group-level MTU overrides

{% hint style="warning" %}
**Availability**

This feature requires an Enterprise plan. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

Group-level overrides let you set a different client MTU for members of specific groups. They are available only when **Assign custom MTU value for this location** is selected.

To add an override, click **Add group-level MTU**, enter the MTU value, and in the next step select the groups it applies to. You can add multiple overrides with different values.

* Users who are not in any of the selected groups use the **Default client MTU**.
* If a user belongs to several groups with different overrides, the **lowest** value is used.
