---
description: How to configure connection between Defguard instance and LDAP.
---

# Configuration

{% hint style="warning" %}
Active Directory support is available in Defguard ≥ v1.3.0
{% endhint %}

{% hint style="warning" %}
If you are using the integration across multiple nested organizational units, please read the [#multiple-nested-ous](configuration.md#multiple-nested-ous "mention") section.
{% endhint %}

## Setup

First, navigate to the settings page and select the LDAP tab.

<figure><img src="../../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

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

After enabling the LDAP integration, you will gain the ability to log in to Defguard through LDAP. Additionally, all your Defguard user changes after you enable the integration will be propagated to LDAP. This is a simple one-way synchronization. If you are interested in synchronizing LDAP and Defguard both ways, check [two-way-ldap-and-active-directory-synchronization.md](two-way-ldap-and-active-directory-synchronization.md "mention").

## Example configurations

### Example Active Directory configuration

<figure><img src="../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

This is an example configuration for a default Active Directory setup on a Windows Server 2022. The most important aspect is setting the "LDAP server is Active Directory" setting, as AD support won't work otherwise. Additionally, `ldaps` has been configured as AD requires an encrypted connection in order for Defguard to be allowed to send user passwords, which is critical if you expect to create users/set passwords through Defguard.

The "cn" attribute has been configured as the user's RDN as that's what used in the user's DN in our example setup (`cn=user1,cn=users,dc=ad,dc=example,dc=com`). This is different from the username attribute, which will be mapped directly to the Defguard username.

### Example OpenLDAP configuration

<figure><img src="../../.gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

This is an example configuration for an OpenLDAP server integrated with Samba (hence `sambaSamAccount` object class). The `inetOrgPerson` has been set as the user structural class which adds attributes to the LDAP user like `email` or `mobile`. `simpleSecurityObject` class has been added for the ability to set passwords in LDAP.



## Known issues

### Multiple nested OUs

Multiple nested organizational units are supported in Defguard 1.4.0 and above.

If you are using an older version of Defguard, using the integration with multiple nested organizational units may currently lead to some unexpected behavior. The following issues are known to occur:

* If you have duplicate user RDNs across multiple OUs a database error may occur: `Duplicate key violates unique constraint 'unique_ldap_rdn'` , causing issues with two-way synchronization. This would happen in the following scenario:
  * `CN=user1,OU=ou1,OU=ou,DC=example`
  * `CN=user1,OU=ou2,OU=ou,DC=example`
*   Limiting synchronization to selected groups may not work if your user's DN doesn't match the user search base:

    * Search base: `OU=ou,DC=example`
    * User's DN: `CN=user1,OU=ou1,OU=ou,DC=example`&#x20;

    In this example, the user's DN has deeper nesting than the search base, preventing matching them during the group members lookup.

To fix this problem, you should limit the search base to one organizational unit only, if possible.

### LDAP users can't go through the enrollment process

By default, if a user is synchronized from LDAP (or logs in through the LDAP integration) they do not go through the enrollment process in the Client when adding their first device. LDAP user already has all the details configured in order to login to Defguard. In some scenarios though it may be necessary to force the user to still go through the enrollment process first, for example, for them to be able to initially set their MFA method in the Client without having access to the Defguard dashboard and their profile yet.

In the 1.5 version, forcing an LDAP user to go through the enrollment process requires the following workaround:

1. You will need to access the database. This can be achieved in several ways:
   1. If your Defguard database is running in a Docker container, for example if you deployed from the one line script, you can use the PSQL client inside the database container:
      1. Execute `sudo docker container ls` to see what containers are running
      2. Copy the container ID or name of the Defguard database container.
      3. Do `sudo docker exec -it <CONTAINER_ID_OR_NAME> psql -U defguard` to gain access to the database
   2. If your database is not running inside a Docker container but as standalone process, you will need to use the PSQL tool directly. If you are on the same server as a database the correct PSQL CLI should already be installed. Otherwise, you will need to install the appropriate version (matching the PostgreSQL version of your Defguard database) of the tool, for example by doing `sudo apt install postgresql-client-17` (for systems using APT) if your PostgreSQL version is 17. After installing the client:&#x20;
      1. Run `psql -h <DATABASE_HOST> -p <DATABASE_PORT> -d defguard -U defguard`&#x20;
      2. You will be prompted for a password. The database password can be found in your Defguard Core environment variables (for example, in it's `.env` file, or `/etc/defguard/core.conf` if you installed from DEB/RPM packages).
2. After you gain access to the database and are in the PSQL prompt, you can execute the following to force given LDAP user to go through the enrollment process:\
   `update "user" set from_ldap = false where username = '<USERNAME>';`\
   You can also match the user by their `email`, if you prefer. And if you want to do this for **all** LDAP users:\
   `update "user" set from_ldap = false where ldap_user_path is not null;`
3. Performing those commands will cause the user to be forced to go through the enrollment process when they try to configure a new device in the Desktop client. This method comes with some caveats:
   1. This won't work if a given user already has a password set in Defguard. Users with password set are always considered to be already enrolled. Typically LDAP users won't have their passwords set in Defguard if they haven't done it manually via a password reset.
   2. If a user performs a login to the Defguard dashboard through LDAP their "from\_ldap" status is again set to "true", marking them back as already enrolled.
   3. During the enrollment process the user will be prompted to enter a new password. This password can be later used for dashboard login but users will be still be able to login through their LDAP credentials.
