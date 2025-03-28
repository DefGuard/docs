---
icon: arrow-right-arrow-left
---

# Two-way LDAP and Active Directory synchronization

{% hint style="warning" %}
This is an alpha feature available in Defguard core v1.3.0 alpha builds and above. It may not be production ready yet and may have some bugs. Report any issues you find on our [GitHub](https://github.com/DefGuard/defguard/issues).
{% endhint %}

{% hint style="danger" %}
Make sure to be aware of the mechanisms described in [#authority-and-full-synchronization](two-way-ldap-and-active-directory-synchronization.md#authority-and-full-synchronization "mention")and [#first-synchronization](two-way-ldap-and-active-directory-synchronization.md#first-synchronization "mention") before enabling this feature, as improper use may cause loss of user data.
{% endhint %}

The LDAP synchronization allows for synchronizing users and groups between Defguard and your LDAP server.

This feature has been mainly tested with OpenLDAP and Active Directory.

## Requirements

* An LDAP server
* One of the following user object classes set for your LDAP users: `user`, `inetOrgPerson`

For Active Directory:

* 128-bit encrypted TLS/SSL connection to the AD server. You can read more about it in the [official guide](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/enable-ldap-over-ssl-3rd-certification-authority) or this step by step [instructions](https://gist.github.com/magnetikonline/0ccdabfec58eb1929c997d22e7341e45).

For OpenLDAP:

* SHA1 as the hashing algorithm and `simpleSecurityObject` user object class

## Setup

First, you will need to configure your LDAP connection. Refer to [configuration.md](configuration.md "mention") for instructions. Here is an example configuration for an OpenLDAP server without SSL/TLS:\


<figure><img src="../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

Make sure you selected "Enable LDAP integration" as without it, the two way synchronization won't work. After you fill out all the fields, test your configuration using the "Test" button.&#x20;

The LDAP two way synchronization has the following options available:

<figure><img src="../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

* **Enable LDAP two-way synchronization** - enables the two way synchronization. Check it if you want to pull changes from LDAP.
* **Consider the following source as the authority** - makes the selected server the source of truth. See [#authority-and-full-synchronization](two-way-ldap-and-active-directory-synchronization.md#authority-and-full-synchronization "mention") for more details.
* **Synchronization interval** - how often (in seconds) to pull LDAP changes

If you enabled the LDAP integration but not the two-way synchronization, your changes in Defguard will be propagated to LDAP but not the other way around.

## Synchronization mechanism overview

The goal of the LDAP two-way synchronization is to make the two data sources (LDAP and Defguard) equal. To achieve this, two variants of synchronization are used: synchronous and asynchronous.

#### Synchronous synchronization

Synchronous synchronization happens every time a change occurs in Defguard, e.g. when a user is modified, added or removed. In this case a respective change is immediately sent to the LDAP server. This synchronization always happens if you select "Enable LDAP integration". It's part of the so called "incremental synchronization".

#### Asynchronous synchronization

Asynchronous synchronization happens periodically in the background and it happens only when you enable the two-way synchronization. This synchronization pulls changes from your LDAP server to be applied in Defguard. The interval of this synchronization may be configured using the "Synchronization interval" setting in the LDAP settings. It's part of the "incremental synchronization".

#### Authority and full synchronization

Authority is the setting allowing you to set which source will be considered as more important or where a change is most likely to occur. You can select the authority based on the following:

* If you are most likely to manage your users in the LDAP server with occasional changes in Defguard, select the LDAP server as the authority.
* If you are most likely to manage users in Defguard, leave Defguard as the authority

The selected authority is used during a full synchronization. This type of synchronization may occur when Defguard assumes that the two sources may have diverged and regular synchronization won't be possible. This can happen in the following scenarios:

* First synchronization after enabling two-way synchronization will always be a full synchronization, since Defguard can't gracefully merge changes that were made before.
* Some issue prevented Defguard from synchronously sending a change to the LDAP server
* You switched the authority or disabled LDAP integration

The full synchronization takes both sources, compares them and produces changes in regard to the given authority. For example, given the following sources:

* **LDAP users**: `user1`, `user2`
* **Defguard** **users**: `user2`, `user3`

With LDAP authority:

* `user3` will be removed from Defguard (since he is not in LDAP),&#x20;
* `user1` will be added to Defguard (since he is not in there but is in LDAP)

With Defguard authority:

* `user1` will be removed from LDAP (since he is not in Defguard)
* `user3` will be added to LDAP (since he is not there but is in Defguard)

This can be summed up as: authority indicates the most likely place where a change occurred, which is the reason of the current state. If the LDAP server is set as the authority, we assume that any difference between Defguard is caused by some change in LDAP which hasn't been reflected yet, so we must perform some actions in Defguard to make it equal.

### First synchronization

The first synchronization will replace all your records with the records of the other source, so it's important to select the direction correctly. This is done by setting the authority, discussed in [#authority-and-full-synchronization](two-way-ldap-and-active-directory-synchronization.md#authority-and-full-synchronization "mention"). In short:

* LDAP → Defguard: If you want to replace all Defguard users with LDAP users, set LDAP as the authority
* Defguard → LDAP: If you want to replace all LDAP users with Defguard users, set Defguard as the authority

### Logging in and passwords

As passwords are stored as hashes with possibly incompatible hashing algorithm between sources, Defguard doesn't synchronize passwords both ways.

#### Defguard → LDAP

Passwords are set in LDAP only on Defguard user account creation, enrollment, password change or reset. Basically when the password is explicitly provided by the user with the intent to set or change it.&#x20;

This means that if you want to import to LDAP all Defguard users who were created before enabling LDAP integration, they will have to change their passwords in Defguard in order for it to be propagated and set in LDAP.&#x20;

Because some LDAP implementations will require password on user creation, Defguard will set a temporary, long, random text as the LDAP user password until it's not changed/set/reset by the user in Defguard.

#### LDAP → Defguard

Defguard doesn't pull passwords from LDAP in any form. Instead, when user tries to login to Defguard, if the LDAP integration is enabled, test login attempt will be made to the LDAP server (bind) with the provided credentials. If the test login attempt succeeds, Defguard will authenticate the user just as during a regular login.

## Known issues

### General

#### Defguard users are losing their groups (e.g. "admin" group)

Your LDAP server may have silently refused creating a Defguard group. A common cause may be a DN conflict, e.g. when the DN for your groups and users has the same structure (`cn=<NAME>,cn=users,dc=example,dc=com` both for users and groups). To solve this, create a new group with a name that won't conflict with any other DN.

Otherwise, report it on our GitHub along with any appropriate logs.

#### Can't edit Defguard user because of invalid username

Your LDAP server may allow for usernames that Defguard doesn't accept, e.g. with spaces. Currently the only way to prevent this from happening is not using such usernames in LDAP if you need the ability to update them in Defguard.

#### Can't login with my LDAP username

If Defguard doesn't accept your LDAP username because it has some invalid characters, try logging in through your email address.

#### Something wasn't updated in LDAP

If you notice that your Defguard change isn't propagated properly to LDAP, run Defguard with debug logs enabled (`DEFGUARD_LOG_LEVEL=debug` environment variable).  Some LDAP errors may be not reported as errors by the LDAP server but most of the operations outputs are logged in the debug logs to help you narrow down the issue.

#### Defguard logs suggest that it uses LDAP authority during synchronization despite setting something different in the settings

Incremental synchronization (as opposed to the full synchronization) internally uses LDAP as the authority. This is only an implementation detail to pull and apply changes from LDAP. The authoritative source you picked in settings is only used during full synchronization.

### Active Directory

#### SysErr: DSID-031A1262, problem 22 (Invalid argument)

You are trying to synchronize a Defguard user with username longer than 20 characters, which [AD doesn't support](https://learn.microsoft.com/en-us/windows/win32/adschema/a-samaccountname?redirectedfrom=MSDN).

