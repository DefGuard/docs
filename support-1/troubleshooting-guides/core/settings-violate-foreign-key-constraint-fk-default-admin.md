# Cannot save settings after deleting the default admin

### Error

Saving settings, for example the LDAP settings, fails and Core logs the following:

{% code overflow="wrap" %}
```bash
error: error returned from database: insert or update on table "settings" violates foreign key constraint "fk_default_admin"
```
{% endcode %}

### Cause

The settings hold a reference to the default admin user. When you delete that user, the database clears the reference, but the running Core keeps the deleted user ID in its in-memory settings cache. The next save writes the deleted ID back, and the database rejects it.

The problem affects Defguard 2.0 only. Defguard 2.1 refreshes the cache after the delete.

### Solution

Restart Core. Core reads the settings from the database at startup, so the cache is correct again and saving works.

For Docker Compose deployments, run the following command in the directory that holds your `docker-compose.yaml`:

```bash
docker compose restart core
```

The problem returns if you delete the default admin again. Upgrade to Defguard 2.1 or later for a permanent fix.
