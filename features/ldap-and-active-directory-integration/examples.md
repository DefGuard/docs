# Example configurations

The examples below show reference configurations for two common directory environments: [OpenLDAP](https://www.openldap.org/) and Active Directory. Use them to validate the overall structure of your setup, but always adjust the exact values, attributes, and search bases to match your own directory layout.

These examples are intended as starting points, not copy-and-paste configurations. Small differences in schema design, distinguished names, search bases, object classes, and TLS setup can affect whether authentication and synchronization work correctly.

Before applying either example, verify the following in your own environment:

* the correct LDAP URL and encryption method,
* the bind account credentials and permissions,
* the user and group search bases,
* the attributes used for usernames, group names, and memberships,
* whether you need one-way or two-way synchronization.

## Example Active Directory configuration

This is an example configuration for a default Active Directory setup on Windows Server 2022. The most important aspect is enabling the "LDAP server is Active Directory" setting, as AD support will not work otherwise. Additionally, `ldaps` has been configured because AD requires an encrypted connection for Defguard to be allowed to send user passwords, which is critical if you expect to create users or set passwords through Defguard.

The `cn` attribute has been configured as the user's RDN because that is what is used in the user's DN in our example setup (`cn=user1,cn=users,dc=ad,dc=example,dc=com`). This is different from the username attribute, which is mapped directly to the Defguard username.

Use this example if your users and groups are managed in a standard on-premise Active Directory structure and you want Defguard to authenticate users against AD and optionally synchronize user data.

* Connection settings
  * Use StartTLS: disabled
  * LDAP server is Active Directory: enabled
  * Verify TLS certificate: enabled
  * URL: use `ldaps` scheme, for example `ldaps://ad.server.name`
  * Bind username: the administrator's DN
  * Bind password: the administrator's password
* User settings
  * Username attribute: `sAMAccountName`
  * User RDN attribute: `cn`
  * User search base: for example `cn=users,dc=ad,dc=server,dc=name`
  * User object class: `user`
  * Member attribute: `memberOf`
  * Additional user object classes: leave empty
* Group settings
  * Groupname attribute: `cn`
  * Group object class: `group`
  * Group member attribute: `member`
  * Group search base: for example `cn=users,dc=ad,dc=server,dc=name`
* LDAP synchronization
  * Two-way synchronization: enable this option if needed

If you enable two-way synchronization for Active Directory, review the synchronization scope and authority settings carefully before using it in production. Those settings determine how Defguard resolves differences between AD and its own database.

## Example OpenLDAP configuration

This is an example configuration for an [OpenLDAP](https://www.openldap.org/) server integrated with [Samba](https://www.samba.org/), hence the `sambaSamAccount` object class. `inetOrgPerson` has been set as the user structural class, which adds attributes to the LDAP user such as `email` or `mobile`. The `simpleSecurityObject` class has been added to allow passwords to be set in LDAP.

Use this example if your deployment is based on a more traditional LDAP schema and you want Defguard to work with OpenLDAP user and group entries directly.

* Connection settings
  * Use StartTLS: as desired
  * LDAP server is Active Directory: disabled
  * Verify TLS certificate: enabled
  * URL: for example `ldaps://ldap.server.name`
  * Bind username: the administrator's DN
  * Bind password: the administrator's password
* User settings
  * Username attribute: `cn`
  * User RDN attribute: empty
  * User search base: for example `cn=users,dc=ldap,dc=server,dc=name`
  * User object class: `inetOrgPerson`
  * Member attribute: `memberOf`
  * Additional user object classes: `simpleSecurityObject, sambaSamAccount`
* Group settings
  * Groupname attribute: `cn`
  * Group object class: `groupOfUniqueNames`
  * Group member attribute: `uniqueMember`
  * Group search base: for example `cn=groups,dc=ldap,dc=server,dc=name`

After applying either example, save the settings and use the connection test in Defguard before enabling synchronization. A successful connection test confirms that Defguard can reach the directory and authenticate with the bind account, but you should still verify that user lookup, group lookup, and any intended synchronization behavior work as expected.
