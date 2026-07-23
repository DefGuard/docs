# Lost access

## CLI troubleshooting

Use the `defguard manage` commands when the Web UI (Check [this article](../../../features/managing-defguard-via-cli.md) for more information)

### Lost access to an admin account

If the only admin forgot their password or lost access, change it via CLI command.

```bash
defguard manage change-password --username <username> --password <new-password>
```

Log in with the new password. If the account also lost its admin group, create new one and set is as admin group:

```bash
defguard manage create-group --name admin_group
defguard manage set-admin-group --name admin_group
```

Add the user back to it:

```bash
defguard manage add-user-to-group --username <username> --name admin_group
```

### Lost admin group due to LDAP/OIDC synchronization

If the admin group was deleted or membership was overwritten by external sync, fix it from the CLI.

First stop external synchronization from overwriting the group again:

*   For OIDC directory sync:

    ```bash
    defguard manage disable-oidc-directory-sync
    ```
*   For LDAP integration:

    ```bash
    defguard manage disable-ldap-integration
    ```

    Restart Defguard after disabling LDAP so the change takes effect on a running server. (Not necessary when used on disabled instance)

Then create a new group and mark it as admin:

```bash
defguard manage create-group --name admins
defguard manage set-admin-group --name admins
```

Add the user back to it:

```bash
defguard manage add-user-to-group --username <username> --name admins
```

