# Configuration

Here you can find list of all configurable things through environmental variables in Defguard core server, YubiBridge client and Gateway client.

## Core server

### LDAP configuration

* `DEFGUARD_LDAP_URL` - Your LDAP URL to read users and groups data (e.g. `http://localhost:389`)
* `DEFGUARD_LDAP_GROUP_MEMBER_ATTR` :  group attribute for members, default: `uniqueMember`
* `DEFGUARD_LDAP_GROUP_SEARCH_BASE` : group search base, default: `ou=groups,dc=example,dc=org`
* `DEFGUARD_LDAP_USER_SEARCH_BASE` : user  search base, default: `dc=example,dc=org`
* `DEFGUARD_LDAP_USER_OBJ_CLASS` :  user object class, default: `inetOrgPerson`
* `DEFGUARD_LDAP_GROUP_OBJ_CLASS` : group object class, default: `groupOfUniqueNames`
* `DEFGUARD_LDAP_ADMIN_GROUPNAME` : group name with administrator privileges, default: `admin`
* `DEFGUARD_LDAP_USERNAME_ATTR` : naming attribute for users, should be `cn` or `uid` , default: `cn`
* `DEFGUARD_LDAP_GROUPNAME_ATTR` : naming attribute for groups, default: `cn`
* `DEFGUARD_LDAP_MEMBER_ATTR` : naming attribute for group membership, default: `memberOf`
* `DEFGUARD_LDAP_GROUP_MEMBER_ATTR` :  group attribute for members, default: `uniqueMember`

### Core configuration

* `DEFGUARD_DATABASE_URL` : Your database URL, default: `sqlite::memory`
* `DEFGUARD_HTTP_PORT` : Core server port, default: `8000`
* `DEFGUARD_AUTH_SECRET` : JWT secret key for encrypting user tokens, default: `DEFGUARD_AUTH_SECRET`
* `DEFGUARD_YUBIBRIDGE_SECRET` : JWT secret key for encrypting YubiBridge tokens, default: `DEFGUARD_YUBIBRIDGE_SECRET`
* `DEFGUARD_GATEWAY_SECRET` : JWT secret key for encrypting Gateway tokens, default: `DEFGUARD_GATEWAY_SECRET`
* `DEFGUARD_WG_SERVICE_URL` : WireGuard service instance to connect to, default: `http://wireguard:50051`
* `DEFGUARD_WEBAUTHN_RP_ID` : Relying party id and relying party origin for WebAuthn used for MFA, default `localhost`
* `DEFGUARD_URL` : Url of your server instance, default `localhost:8000`, Needed for OpenID discovery endpoint to work correctly.
* `DEFGUARD_OPENID_KEY` : Path to private key file used for OAuth2/OpenID, [more here](https://defguard.gitbook.io/defguard/features/setting-up-your-instance/docker-compose#openid-rsa-setup)

### gRPC configuration

* `DEFGUARD_GRPC_PORT` : gRPC server port, default `50055`

## YubiBridge configuration

### Environmental variables

* `LOG_LEVEL` : Log messages level, default: `INFO`, available levels: `CRITICAL`, `ERROR`, `WARNIG`, `INFO`, `DEBUG`
* `WORKER_ID` : Name of your YubiBridge displayed on Defguard website, default: `YubiBridge`
* `DEFGUARD_TOKEN` : - Secret worker token to secure gRPC communication, available on provisioners page
* `SMARTCARD_RETRIES` : Number of retries in case provisioning failed, default: `1`
* `JOB_INTERVAL` : Defines how often(seconds) YubiBridge checks Defguard for new jobs, default: `2`
* `SMARTCARD_RETRY_INTERVAL` : Defines number of seconds between trying to provision YubiKey again, default `15`

### CLI arguments:

* `-h` , `--help` : Display help message
* `-g <URL>`, `--grpc <URL>` : Connect to gRPC server at given URL
* `-i <ID>` , `--id <ID>` : WorkerID, default `YubiBridge`   
* `-d` , `--debug` : Enable debug mode
* `-t <TMPDIR>` , `--tmpdir <TMPDIR>` : GnuPG home directory, default: `tmp`
* `-p <first_name> <last_name> <email>` , `--provision <first_name> <last_name> <email>` : Provision YubiKey with following data  
* `-w <token>` , `--worker-token <token>` : Secret worker token to secure gRPC communication, available on provisioners page
* `-c <command>` , `--command <command>` : Run command after provisioning and pass created keys as arguments

## Gateway Configuration

### Environmental variables / Arguments

If you're using docker image you can pass this value as environmental variables or on binary you can pass them as arguments

`DEFGUARD_USERSPACE` , `-u` - Use userspace wireguard implementation, useful on systems without native wireguard support

`DEFGUARD_GRPC_URL` , `-g <URL>` - Defguard server gRPC endpoint URL default is https://localhost:50055

`DEFGUARD_STATS_PERIOD` ,`-p <SECONDS>` - Defines how often (seconds) should interface statistics be sent to Defguard server

`DEFGUARD_TOKEN` ,`-t <TOKEN>` - Token received on Defguard after completing network wizard

