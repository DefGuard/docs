---
icon: arrow-right-arrow-left
---

# Two-way LDAP and Active Directory synchronization

{% hint style="danger" %}
Make sure to be aware of the mechanisms described in [#authority-and-full-synchronization](two-way-ldap-and-active-directory-synchronization.md#authority-and-full-synchronization "mention") and [#first-synchronization](two-way-ldap-and-active-directory-synchronization.md#first-synchronization "mention") before enabling this feature.\
\
We recommend testing the integration first in a non-production environment, as improper configuration may cause loss of user data.
{% endhint %}

The LDAP synchronization allows for synchronizing users and groups between Defguard and your LDAP server.

This feature has been mainly tested with OpenLDAP and Active Directory.

## Requirements

* An LDAP server
* One of the following user object classes set for your LDAP users: `user`, `inetOrgPerson`
* The following attributes set for users you want to sync: username attribute (e.g. `sAMAccountName` or `cn`), `sn`, `givenName`, `mail` - those are required by Defguard
* Username attribute must conform to the restrictions specified in [settings-table.md](settings-table.md "mention")

For Active Directory:

* 128-bit encrypted TLS/SSL connection to the AD server. You can read more about it in the [official guide](https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/enable-ldap-over-ssl-3rd-certification-authority) or this step by step [instructions](https://gist.github.com/magnetikonline/0ccdabfec58eb1929c997d22e7341e45).

For OpenLDAP:

* SHA1 as the hashing algorithm and `simpleSecurityObject` user object class

## Setup

First, you will need to configure your LDAP connection. Refer to [configuration.md](configuration.md "mention") for instructions. Here is an example configuration for an OpenLDAP server without SSL/TLS:

<figure><img src="../../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

Make sure you selected "Enable LDAP integration" as without it, the two way synchronization won't work. After you fill out all the fields, test your configuration using the <img src="../../../.gitbook/assets/image (2).png" alt="" data-size="line"> button.&#x20;

The LDAP two way synchronization has the following options available:

<figure><img src="../../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

* **Enable LDAP two-way synchronization** - enables the two way synchronization. Check it if you want to pull changes from LDAP.
* **Consider the following source as the authority** - makes the selected server the source of truth. See [#authority-and-full-synchronization](two-way-ldap-and-active-directory-synchronization.md#authority-and-full-synchronization "mention") for more details.
* **Synchronization interval** - how often (in seconds) to pull LDAP changes

If you enabled the LDAP integration but not the two-way synchronization, your changes in Defguard will be propagated to LDAP but not the other way around.

### Selecting which users to synchronize

{% hint style="warning" %}
Before enabling this feature, check if you meet requirements described in [settings-table.md](settings-table.md "mention")
{% endhint %}

If you want to synchronize only selected users, you can specify the groups of which members should be synchronized.

<figure><img src="../../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

This can be useful if you have a lot of users in your LDAP server and want to synchronize/pull only users belonging to a given group, e.g. `defguard-sync`.

This setting is described in more depth in [settings-table.md](settings-table.md "mention") and affects both LDAP → Defguard and Defguard → LDAP synchronizations.

After specifying synchronization groups, only members of those groups will be kept in sync.

#### Pruning users after changing the synchronization groups

{% hint style="info" %}
The following advice should be applied only when you are using LDAP as the authoritative server.
{% endhint %}

After you change your synchronization groups, users not belonging to the new groups won't be automatically deleted. This may be an issue if you first used the two way synchronization without any synchronization groups, effectively synchronizing everyone and decided later to narrow the scope of synchronization. This can result in many redundant, not synchronized user records in your Defguard instance laying around. If you want to prune your Defguard users to only those who are in your synchronization group, you can follow these steps (assuming you have already set your synchronization groups):

1. Wait for a two-way periodic synchronization to complete, you can recognize it by the `LDAP sync completed` log message.
2. Temporarily disable the whole LDAP integration in the settings ![](<../../../.gitbook/assets/image (96).png>)
3. In the Defguard user's list, bulk assign all users one of your synchronization groups, to bring them into the scope of synchronization. You may want to leave out all users which you don't want to be ever touched by the LDAP integration, e.g. the default admin user or other users you want to keep only in Defguard.
4. Enable the LDAP integration in the settings <img src="../../../.gitbook/assets/image (98).png" alt="" data-size="line">
5. Now, the next two-way synchronization will remove all users from Defguard who have the synchronization group you just assigned in Defguard but don't have it in LDAP, effectively leaving you only with users that have the group in both sources.

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

## Known issues and other unexpected behavior

### General

#### Groups are not being synced from LDAP

Only non-empty groups are currently synchronized, groups that don't have any syncable members won't be created in Defguard.

#### A user is not being synced

Your user may be missing one of the required attributes. Check [#requirements](two-way-ldap-and-active-directory-synchronization.md#requirements "mention") for a full list. User without one of those attributes will be skipped.

#### Defguard users are losing their groups (e.g. "admin" group)

Your LDAP server may have silently refused creating a Defguard admin group. A common cause may be a DN conflict, e.g. when the DN for your groups and users has the same structure (`cn=<NAME>,cn=users,dc=example,dc=com` both for users and groups). To solve this, create a new group with a name that won't conflict with any other DN.

Otherwise, report it on our GitHub along with any appropriate logs.

#### Something wasn't updated in LDAP

If you notice that your Defguard change isn't propagated properly to LDAP, run Defguard with debug logs enabled (`DEFGUARD_LOG_LEVEL=debug` environment variable).  Some LDAP errors may be not reported as errors by the LDAP server but most of the operations outputs are logged in the debug logs to help you narrow down the issue.

#### Defguard logs suggest that it uses LDAP authority during synchronization despite setting something different in the settings

Incremental synchronization (as opposed to the full synchronization) internally uses LDAP as the authority. This is only an implementation detail to pull and apply changes from LDAP. The authoritative source you picked in settings is only used during full synchronization.

### Active Directory

#### SysErr: DSID-031A1262, problem 22 (Invalid argument)

You are trying to synchronize a Defguard user with username longer than 20 characters, which [AD doesn't support](https://learn.microsoft.com/en-us/windows/win32/adschema/a-samaccountname?redirectedfrom=MSDN).

