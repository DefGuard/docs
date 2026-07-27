# Managing Defguard via CLI

Use the `defguard manage` commands when the Web UI or API become unavailable because of user or group problems. This tool connects directly to the database, you need to run it with the same [database settings](managing-defguard-via-cli.md#setting-database-credentials) as the running Defguard server.

### What `defguard manage` offers

* `create-admin` - Create a new admin user account and add it to an admin group.
* `change-password` - Change a user's password.
* `list-users` - List all users.
* `set-admin-group` - Mark an existing group as a Defguard admin group, granting its members admin privileges.
* `create-group` - Create a new group.
* `list-groups` - List all groups and their admin status.
* `add-user-to-group` - Add a user to a group by username.
* `disable-ldap-integration` - Disable the LDAP integration.
* `disable-oidc-directory-sync` - Disable directory synchronization for the configured OIDC external identity provider.

Run `defguard manage --help` to see the full help, or `defguard manage <command> --help` for a specific command.

### Setting database credentials

If your database is not on `localhost:5432`, pass the connection parameters before `manage`:

<pre class="language-bash"><code class="lang-bash"><strong>##example usage
</strong><strong>./defguard \
</strong>  --database-host &#x3C;DATABASE_HOST> \
  --database-port &#x3C;DATABASE_PORT> \
  --database-name &#x3C;DATABASE_NAME> \
  --database-user &#x3C;DATABASE_USER> \
  --database-password &#x3C;DATABASE_PASSWORD> \
  manage change-password \
  --username &#x3C;username> \
  --password &#x3C;new-password>
</code></pre>

The database flags must come before the `manage` subcommand. The command specific flags, such as `--username` and `--password`, come after it.

## Example usage

### Create a new admin when no admin exists

If there is no admin account at all, create one and add it to an existing admin group:

```bash
defguard manage create-admin --username <username> --password <password> --group-name <admin-group>
```

If no admin group exists either, create the group first and mark it as admin:

```bash
defguard manage create-group --name admins
defguard manage set-admin-group --group-name admins
defguard manage create-admin --username <username> --password <password> --group-name admins
```

{% hint style="info" %}
If you won't specify `--group-name <admin-group>` new user will be assigned to the first admin group found.
{% endhint %}

### Promote an existing user to admin

Add the user to the admin group:

```bash
defguard manage add-user-to-group --username <username> --group-name <admin-group>
```

### Check who is admin

```bash
defguard manage list-users

##example output
Username     | Admin privileges
admin        | true
alice        | false
john         | false
peter        | false
```

### &#x20;Check which group is admin

```bash
defguard manage list-groups

##example output
ID | Group name     | Admin privileges
2  | first          | false
3  | second         | false
7  | test_group     | true
8  | admin          | true
```

### Stop directory sync

If OIDC or LDAP sync keeps removing admin rights or recreating unwanted groups which prevents you from accesing Web UI, disable the sync first, fix the membership from the CLI, then reconfigure the directory source.

For OIDC:

```bash
defguard manage disable-oidc-directory-sync
```

For LDAP:

```bash
defguard manage disable-ldap-integration
## If Defguard was running when this was executed, restart it. 
```

### Use group ID instead of group name

All commands where you need to specify group, accept `--group-id` instead of `--group-name`. Run `defguard manage list-groups` to see the IDs.

```bash
##example usage
defguard manage set-admin-group --group-id <id>
defguard manage add-user-to-group --username <username> --group-id <id>
```
