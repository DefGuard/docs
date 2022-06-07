# Configuration

Here you can find list of all configurable things through environmental variables in Defguard core server, YubiBridge client and Gateway client.

### LDAP configuration

* `DEFGUARD_LDAP_URL` - Your LDAP url to read users and groups data (e.g. `http://localhost:389`)
* `DEFGUARD_LDAP_GROUP_MEMBER_ATTR` :  group attribute for members, default: `uniqueMember`
* `DEFGUARD_LDAP_GROUP_SEARCH_BASE` : group search base, default: `ou=groups,dc=example,dc=org`
* `DEFGUARD_LDAP_USER_SEARCH_BASE` : user  search base, default: `dc=example,dc=org`
* `DEFGUARD_LDAP_USER_OBJ_CLASS` :  user object class, default: `inetOrgPerson`
* `DEFGUARD_LDAP_GROUP_OBJ_CLASS` : group object class, default: `groupOfUniqueNames`
* `DEFGUARD_LDAP_ADMIN_GROUPNAME` : group name with administrator privileges, default: `admin`
* `DEFGUARD_LDAP_USERNAME_ATTR` : naming attribute for users, should be `cn` or `uid` , default: `cn`&#x20;
* `DEFGUARD_LDAP_GROUPNAME_ATTR` : naming attribute for groups, default: `cn`
* `DEFGUARD_LDAP_MEMBER_ATTR` : naming attribute for group membership, default: `memberOf`&#x20;
* `DEFGUARD_LDAP_GROUP_MEMBER_ATTR` :  group attribute for members, default: `uniqueMember`

### Core configuration

* `DEFGUARD_DATABASE_URL` : Your database url, default: `sqlite::memory`&#x20;
* `DEFGUARD_HTTP_PORT` : Core server port, default: `8000`&#x20;
* `DEFGUARD_JWT_SECRET` : JWT secret key for encrypting tokens, default: `secret`
* `ORI_WG_SERVICE_URL` : WireGuard service instance to connect to, default: `http://wireguard:50051`

### GRPC configuration

* `DEFGUARD_GRPC_PORT` : GRPC server port, default `50055`

### YubiBridge

*
