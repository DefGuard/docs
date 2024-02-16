---
description: How to configure connection between Defguard instance and LDAP.
---

# Configuration

## Setup

First, navigate to the settings page and select the LDAP tab.

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Defguard settings page</p></figcaption></figure>

Now change fields according to your LDAP instance.

{% hint style="info" %}
You can find more brief explanations for these settings on this [page](settings-table.md).
{% endhint %}

After you save your LDAP settings, you can check if your Defguard instance can connect and authenticate to your LDAP server via the "Test" button.

{% hint style="warning" %}
Testing your connection doesn't mean the whole configuration is correct. Currently, Defguard only verifies if a connection can be made and the provided credentials are correct. This will most likely change in future versions of Defguard.
{% endhint %}
