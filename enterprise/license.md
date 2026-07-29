# Purchasing and using the license

Defguard is available as an open-source solution with additional paid features. Some functionalities are available only with a valid license or are subject to usage limits, depending on the selected plan. Details about feature availability, limits, and pricing are provided on the [Pricing page](https://defguard.net/pricing/).

### Plans and limits

Paid features are gated by two plan levels:

* **Business** - unlocks the paid feature set, for example the REST API, LDAP and Active Directory integration, external OpenID providers, the firewall (ACL), activity log streaming and client behaviour customisation.
* **Enterprise** - everything from Business, plus features intended for larger deployments, such as Service Locations, device posture verification, generating Allowed IPs from firewall rules, and component high availability.

Every feature page in this documentation states which plans include it, in an **Availability** note at the top.

{% hint style="info" %}
Enterprise is a **higher** level than Business, so an Enterprise license also unlocks everything that a Business license unlocks.
{% endhint %}

Your license also carries **usage limits** - the number of users and the number of VPN locations, and optionally device counts.
The current tier, expiry date, and how close you are to each limit are shown in **Settings → License**.

{% hint style="success" %}
Small deployments can use a **free Business license**: [https://defguard.net/get-free-business/](https://defguard.net/get-free-business/). The exact limits of the free plan are listed on that page and on the [Pricing page](https://defguard.net/pricing/).
{% endhint %}

### Purchasing the license

If you would like to purchase a license, we offer two types of licenses:

1. **Subscription** that can be bought on [https://defguard.net/pricing/](https://defguard.net/pricing/)
2. **Offline (which will not contact our license server) with a defined custom period** can be bought directly, please contact: sales @ defguard.net

#### Subscription

You can buy a monthly or yearly subscription on our website: [https://defguard.net/pricing/](https://defguard.net/pricing/).

After purchasing:

1. The license will be emailed to you on the email defined in the purchase form. Also there will be a second email with the invoice.
2. Each month (on the date the license expires) **Defguard core will contact our licensing server** and if the monthly payment was successful, our licensing server will **automatically issue a new license and the enterprise plan will be extended to new date.**

{% hint style="warning" %}
If your setup / firewall / network policy **doesn't allow that Defguard will contact our licensing server, please contact us for the** [**Offline license**](license.md#offline-license)**.**
{% endhint %}

#### Offline license

Defguard is build with the highest security architecture in mind, thus there may be scenarios where you don't want any of the components to contact external services (eg. Defguard core will have no access to Internet).

Offline license can be also issued for any period of time, so another scenario is that you can buy the enterprise license for any duration you wish.

To obtain an offline Enterprise License please contact our sales at: **sales \[ a t ] defguard.net** and provide:

* the period for which you would like to obtain the license
* your company data and contact email address (for billing and license sharing)
* preferred payment method: bank wire transfer or card payment link

### Configuring Defguard with obtained license

To configure Defguard with the received license, please go to **Settings** -> License -> Enter license.

<figure><img src="../.gitbook/assets/image (225).png" alt=""><figcaption></figcaption></figure>

The license will be validated and detailed information about the license will be displayed.

<figure><img src="../.gitbook/assets/image (226).png" alt=""><figcaption></figcaption></figure>

### License expiry

A **subscription** license is renewed automatically, so under normal circumstances you do not need to do anything. If a renewal does not go through - for example because Core temporarily cannot reach our licensing server - the license keeps working for a grace period of **14 days** past its expiry date, which gives you time to resolve the problem without losing access to paid features.

An **offline** license has no grace period: it stops being valid on its expiry date, because there is no licensing server to renew it against.

{% hint style="warning" %}
If a license expires past the grace period paid features stop working. Your data, users, devices, and VPN locations are not removed.
{% endhint %}
