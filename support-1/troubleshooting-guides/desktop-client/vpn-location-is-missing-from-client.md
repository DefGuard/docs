# VPN location is missing from client

If a VPN location does not appear in Defguard, one of these causes is usually responsible.

### Client does not support the location

Locations protected by posture checks require Defguard Client `2.1` or later.

If your organization enabled posture checks on a location, that location does not appear in clients older than `2.1`.

The same applies to service locations. They are hidden when the client does not support them, for example on non-Windows platforms or in older clients.

**Fix:** Update Defguard to the latest version, then reopen the app. If the missing location is a service location, use Defguard Client for Windows.

### Client configuration is outdated

If a location was added recently, or your access changed, the client may still be using older instance data.

#### Desktop Client

Update the instance configuration. See [Instance configuration](../../../using-defguard-for-end-users/desktop-client/instance-configuration.md).

#### Mobile Client

Refresh the instance screen manually or restart the app.

See [Mobile Client](../../../using-defguard-for-end-users/mobile-client/) for mobile client behavior.

### You do not have access to the location

Administrators can restrict a location to specific users or groups.

If other users can see the location but you cannot, contact your administrator.

### The location is hidden by design

Some location types are not shown in the regular client list.

For example, Windows service locations run in the background and are hidden in the UI after configuration is applied.
