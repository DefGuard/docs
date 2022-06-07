# Configuration

Here you can find list of all configurable things through environmental variables in Defguard core server, YubiBridge client and Gateway client.

## Core server

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

## YubiBridge

### Environmental variables:

* `LOG_LEVEL` : Log messages level, default: `INFO` available levels: `CRITICAL`, `ERROR`, `WARNIG` ,  `INFO` , `DEBUG` ,
* `WORKER_ID` : Name of your YubiBridge displayed on Defguard website, default: `YubiBridge`&#x20;
* `SMARTCARD_RETRIES` : Number of retries in case provisioning failed, default: `1`&#x20;
* `JOB_INTERVAL` : Defines how often(seconds) YubiBridge checks Defguard for new jobs, default: `2`&#x20;
* `SMARTCARD_RETRY_INTERVAL` : Defines number of seconds between trying to provision YubiKey again, default `15`&#x20;

### CLI arguments:

* `-h` , `--help` : Display help message
* `-g <URL>`, `--grpc <URL>` : Connect to GRPC server at given URL
* `-i <ID>` , `--id <ID>` : WorkerID, default `YubiBridge`   &#x20;
* `-d` , `--debug` : Enable debug mode
* `-t <TMPDIR>` , `--tmpdir <TMPDIR>` : GnuPG home directory, default: `tmp`&#x20;
* `-p <first_name> <last_name> <email>` , `--provision <first_name> <last_name> <email>` : Provision YubiKey with following data  &#x20;
* `-w <token>` , `--worker-token <token>` : Secret worker token to secure gRPC communication
* `-c <command>` , `--command <command>` : Run command after provisioning and pass created keys as arguments

## Gateway Configuration

### Environmental variables / Arguments

If you're using docker image you can pass this value as environmental variables or on binary you can pass them as arguments

`DEFGUARD_USERSPACE` , `-u` - Use userspace wireguard implementation, useful on systems without native wireguard support&#x20;

`DEFGUARD_GRPC_URL` , `-g <URL>` - Defguard server GRPC endpoint URL default is https://localhost:50055

`DEFGUARD_STATS_PERIOD` ,`-p <SECONDS>` - Defines how often (seconds) should interface statistics be sent to Defguard server

`DEFGUARD_TOKEN` ,`-t <TOKEN>` - Token received on Defguard after completing network wizard

