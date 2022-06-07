# LDAP synchronization setup

Ldap synchronization is done by providing necessary environmental variables which allows to connect to your LDAP server and perform certain actions. You can easily synchronize users and groups with almost no time investment.

If you provide environmental variables related to ldap Defguard will automatically synchronize data from your ldap server on start then all your users can login with the same credentials, also all newly created users will be added to your ldap server.



### Ldap Environmental variables

* `DEFGUARD_LDAP_URL` - Your LDAP url to read users and groups data (e.g. `http://localhost:389`)
* `DEFGUARD_LDAP_GROUP_SEARCH_BASE` : group search base, default: `ou=groups,dc=example,dc=org`
* `DEFGUARD_LDAP_USER_SEARCH_BASE` : user  search base, default: `dc=example,dc=org`
* `DEFGUARD_LDAP_USER_OBJ_CLASS` :  user object class, default: `inetOrgPerson`
* `DEFGUARD_LDAP_GROUP_OBJ_CLASS` : group object class, default: `groupOfUniqueNames`
* `DEFGUARD_LDAP_ADMIN_GROUPNAME` : group name with administrator privileges, default: `admin`
* `DEFGUARD_LDAP_USERNAME_ATTR` : naming attribute for users, should be `cn` or `uid` , default: `cn`&#x20;
* `DEFGUARD_LDAP_GROUPNAME_ATTR` : naming attribute for groups, default: `cn`
* `DEFGUARD_LDAP_MEMBER_ATTR` : naming attribute for group membership, default: `memberOf`&#x20;
* `DEFGUARD_LDAP_GROUP_MEMBER_ATTR` :  group attribute for members, default: `uniqueMember`
