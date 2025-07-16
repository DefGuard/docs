# LDAP and Active Directory integration

{% hint style="warning" %}
This is an enterprise feature. To use it, purchase our [enterprise license](../../enterprise/license.md) or ensure that your deployment does not exceed the [usage limits](../../enterprise/license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

defguard supports integration with LDAP and Microsoft Active Directory (AD), enabling seamless connectivity with your existing directory infrastructure. This integration allows organizations to centralize user management, streamline authentication processes, and synchronize user and group data between Defguard and external directory services.

This chapter covers all aspects of LDAP and AD integration, including:

* **Connection Configuration**: How to connect Defguard to your directory server.
* **Settings Overview**: A detailed breakdown of each LDAP configuration option and how it affects synchronization and user mapping.
* **Two-Way Sync**: How Defguard synchronises data both from and to the directory, including how to handle conflicts, deletion policies, and attribute mappings.
