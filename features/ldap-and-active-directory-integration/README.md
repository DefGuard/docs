---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/ldap-and-active-directory-integration
---

# LDAP and Active Directory integration

{% hint style="warning" %}
**Availability**

This feature is available in Business and Enterprise plans. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

{% embed url="https://youtu.be/8lf9UIAt9Rw" %}

Defguard supports integration with LDAP and Microsoft Active Directory (AD), enabling seamless connectivity with your existing directory infrastructure. This integration allows organizations to centralize user management, streamline authentication processes, and synchronize user and group data between Defguard and external directory services.

This feature is especially useful when Defguard needs to fit into an existing identity environment instead of becoming a separate user directory. By connecting Defguard to LDAP or Active Directory, you can reuse your current directory structure, reduce duplicate account management, and keep access data aligned across systems.

Depending on your setup, Defguard can be used in two main ways:

* as an authentication layer that allows users to sign in with directory-backed credentials,
* as a synchronization layer that exchanges user and group data between Defguard and your directory service.

You can start with a simple connection and one-way propagation from Defguard to LDAP, and then move to two-way synchronization if you need Defguard and the external directory to stay aligned over time. This makes it possible to adopt the integration gradually and validate the configuration before enabling more powerful synchronization behaviour.

This chapter covers all aspects of LDAP and AD integration, including:

* **Connection Configuration**: How to connect Defguard to your directory server.
* **Settings Overview**: A detailed breakdown of each LDAP configuration option and how it affects synchronization and user mapping.
* **Two-Way Sync**: How Defguard synchronizes data both from and to the directory, including how to handle conflicts, deletion policies, and attribute mappings.

Before enabling synchronization in production, make sure you understand which system should act as the source of truth, which users should be included in scope, and how the first synchronization behaves. These choices directly affect how users are created, updated, or removed.

Use the pages in this section in the following order:

1. Configure the LDAP or Active Directory connection.
2. Review the settings reference to understand mapping and synchronization options.
3. Enable and test two-way synchronization only after validating the initial connection and scope.
