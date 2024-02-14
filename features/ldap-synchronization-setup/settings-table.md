---
description: List with description of settings for LDAP found in settings page.
---

# Settings table



| Field                  | Description                                              | Default                     |
| ---------------------- | -------------------------------------------------------- | --------------------------- |
| URL                    | URL that points to your LDAP server.                     | empty                       |
| Bind Username          | Bind DN used for authentication.                         | cn=admin,dc=example,dc=org  |
| Bind Password          | Password used for authentication.                        | empty                       |
| Member Attribute       | Naming attribute for group membership.                   | memberOf                    |
| Username Attribute     | Naming attribute for users.                              | cn                          |
| User Search Base       | Relative Distinguished Name (RDN) of your user entries.  | ou=users,dc=example,dc=org  |
| User Object Class      | Object class used for user entries.                      | inetOrgPerson               |
| Groupname Attribute    | Naming attribute for groups.                             | cn                          |
| Group Object Class     | Object class used for group entries.                     | groupOfUniqueNames          |
| Group Member Attribute | Naming attribute for group membership.                   | uniqueMember                |
| Group Search Base      | Relative Distinguished Name (RDN) of your group entries. | ou=groups,dc=example,dc=org |
