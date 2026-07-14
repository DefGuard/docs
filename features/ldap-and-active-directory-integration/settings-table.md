---
description: A list describing the LDAP settings available on the settings page.
---

# Settings table

{% hint style="warning" %}
Ensure that the letter casing in your Defguard settings matches exactly with your LDAP configuration. For instance, if your LDAP uses `CN`, be sure to enter it as `CN` in the settings, not `cn`.
{% endhint %}

| Field                                 | Description                                                                                                                                         | Default                                   |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| URL                                   | The URL of your LDAP server.                                                                                                                        | Empty                                     |
| Bind Username                         | Bind DN used for authentication.                                                                                                                    | cn=admin,dc=example,dc=org                |
| Bind Password                         | Password used for authentication.                                                                                                                   | Empty                                     |
| Member Attribute                      | Naming attribute for group membership.                                                                                                              | memberOf                                  |
| Username Attribute                    | The attribute that will be used as the user's username.                                                                                             | cn                                        |
| User Search Base                      | Relative Distinguished Name (RDN) of your user entries.                                                                                             | ou=users,dc=example,dc=org                |
| User Object Class                     | Object class used for user entries.                                                                                                                 | inetOrgPerson                             |
| Additional User Object Classes        | Auxiliary classes for user entries.                                                                                                                 | simpleSecurityObject, sambaSamAccount     |
| Groupname Attribute                   | Naming attribute for groups.                                                                                                                        | cn                                        |
| Group Object Class                    | Object class used for group entries.                                                                                                                | groupOfUniqueNames                        |
| Group Member Attribute                | Naming attribute for group membership.                                                                                                              | uniqueMember                              |
| Group Search Base                     | Relative Distinguished Name (RDN) of your group entries.                                                                                            | ou=groups,dc=example,dc=org               |
| User RDN attribute                    | The attribute that is part of the user's DN (the leftmost component of the DN).                                                                     | Empty, defaults to the username attribute |
| Limit synchronization to these groups | Limits all LDAP actions only to users belonging to one of the specified groups, both ways. Values should be provided as a list separated by commas. | Empty                                     |

## Settings in depth

There are a few settings that may not be obvious:

* `Additional User Object Classes`: User object classes that will be assigned to a user and will also define the assigned attributes. For example, `simpleSecurityObject` will make users possess the `userPassword` attribute.
* `User Object Class`: The structural class of your users. Just like the additional user object classes, it defines the added attributes, but it is also used during user search. Defguard will only consider entries with this class to be users.

{% hint style="danger" %}
Changing the RDN attribute may cause your users to be re-added to Defguard, causing potential loss of Defguard-specific user data, e.g. their device information.
{% endhint %}

* `User RDN attribute`: The attribute used in your user's DN. It is used to link users between LDAP and Defguard. Depending on your setup, it may be different from the attribute used for usernames. If left empty, your username attribute will be used instead. For example:\
  Given a user DN of `cn=user1,cn=users,dc=ad,dc=example,dc=com`, you would set the RDN attribute to `cn`.
* `Username attribute`: The username attribute, which will be used to set the username of a Defguard user. The following restrictions apply:
  * Only alphanumeric characters except for <kbd>.</kbd>, <kbd>-</kbd> or <kbd>\_</kbd>
  * At least 1 and at most 64 characters
  * Must be unique across all users

{% hint style="danger" %}
To use this feature, your LDAP user entries must possess the `memberOf` attribute (or its equivalent, defined using the member attribute), which may not be available by default on your LDAP server. This may require enabling an appropriate module.
{% endhint %}

* `Limit synchronization to these groups`: Limits the synchronization scope to only the members of the selected groups. This works both ways:
  * Changes in Defguard will be propagated to LDAP only if a user belongs to a given group in Defguard.
  * If the two-way synchronization is enabled, only the users belonging to the specified groups will be fetched from the LDAP server.
  * Adding a user to one of the synchronization groups in Defguard will automatically create that user in LDAP if they do not exist yet. If they already exist, their LDAP data, e.g. the email address, will be overwritten with the data from Defguard if only one-way synchronization (Defguard → LDAP) is enabled. Otherwise, if two-way synchronization is enabled, the selected authority source will be respected.
