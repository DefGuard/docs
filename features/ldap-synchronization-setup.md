# LDAP synchronization

Defguard can be configured to apply all user changes to an LDAP instance.

{% hint style="warning" %}
The integration between Defguard and LDAP is designed to reflect changes made within Defguard in LDAP.
However, changes made directly in LDAP will not be automatically imported into Defguard. 
{% endhint %}

LDAP integration setup is done by providing necessary environment variables.
At minimum you'll have to provide:

* `DEFGUARD_LDAP_URL` - Your LDAP URL to read users and groups data (e.g. `ldap://localhost:389`)
* `DEFGUARD_LDAP_BIND_USERNAME` - Your LDAP admin user (e.g. `cn=admin,dc=example,dc=org`)
* `DEFGUARD_LDAP_BIND_PASSWORD` - Your LDAP admin password

Other LDAP variables:

* `DEFGUARD_LDAP_USER_SEARCH_BASE`: Relative Distinguished Name (RDN) of your user entries, default: `ou=users,dc=example,dc=org`
* `DEFGUARD_LDAP_GROUP_SEARCH_BASE`: Relative Distinguished Name (RDN) of your group entries, default: `ou=groups,dc=example,dc=org`
* `DEFGUARD_LDAP_USER_OBJ_CLASS`: Object class used for user entries, default: `inetOrgPerson`
* `DEFGUARD_LDAP_GROUP_OBJ_CLASS`: Object class used for group entries, default: `groupOfUniqueNames`
* `DEFGUARD_LDAP_USERNAME_ATTR`: Naming attribute for users, should be `cn` or `uid` , default: `cn`
* `DEFGUARD_LDAP_GROUPNAME_ATTR`: Naming attribute for groups, default: `cn`
* `DEFGUARD_LDAP_MEMBER_ATTR`: Naming attribute for group membership, default: `memberOf`
* `DEFGUARD_LDAP_GROUP_MEMBER_ATTR`:  Group attribute for members, default: `uniqueMember`
