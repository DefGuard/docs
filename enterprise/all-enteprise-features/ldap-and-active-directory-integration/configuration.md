---
description: How to configure connection between Defguard instance and LDAP.
---

# Configuration

{% hint style="info" %}
Active Directory support is available in Defguard v1.3.0 and above.
{% endhint %}

## Setup

First, navigate to the settings page and select the LDAP tab.

<figure><img src="../../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

Now change fields according to your LDAP instance.

For an LDAP server with TLS/SSL you may want to configure one of the options related to TLS. Check "Use StartTLS" if your LDAP server uses StartTLS for encrypted connections, alternatively you may also use `ldaps` in the URL field. If you don't want to provide Defguard with your LDAP server's certificate, you may also disable checking it here.

If you are trying to connect to Active Directory, check "LDAP server is Active Directory". Make sure to read [#example-active-directory-configuration](configuration.md#example-active-directory-configuration "mention") for a working example.

{% hint style="info" %}
You can find more brief explanations for these settings on this [page](settings-table.md).
{% endhint %}

After you save your LDAP settings, you can check if your Defguard instance can connect and authenticate to your LDAP server via the "Test" button.

{% hint style="warning" %}
Testing your connection doesn't mean the whole configuration is correct. Currently, Defguard only verifies if a connection can be made and the provided credentials are correct.
{% endhint %}

After enabling the LDAP integration you will gain the ability to login to Defguard through LDAP. Additionally, all your Defguard user changes after you enable the integration will be propagated to LDAP. This is a simple one-way synchronization. If you are interested in synchronizing LDAP and Defguard both ways, check [two-way-ldap-and-active-directory-synchronization.md](two-way-ldap-and-active-directory-synchronization.md "mention").

## Example configurations

### Example Active Directory configuration

<figure><img src="../../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

This is an example configuration for a default Active Directory setup on a Windows Server 2022. The most important aspect is setting the "LDAP server is Active Directory" setting as AD support won't work otherwise. Additionally, `ldaps` has been configured as AD requires an encrypted connection in order for Defguard to be allowed to send user passwords, which is important if you expect to create users/set passwords through Defguard.
