# VPN location is missing from client

If a VPN location does not appear in Defguard, one of these causes is usually responsible.

### Client does not support the location

Locations protected by posture checks require Defguard Client `2.1` or later.

If your organization enabled posture checks on a location, that location does not appear in clients older than `2.1`.

**Fix:** Update Defguard to the latest version, then reopen the app.

### Client configuration is outdated

If a location was added recently, or your access changed, the client may still be using older instance data.

#### Desktop Client

Update the instance configuration. See [Instance configuration](../../../using-defguard-for-end-users/desktop-client/instance-configuration.md).

#### Mobile Client

Refresh the instance screen manually or restart the app.

See [Mobile Client](../../../using-defguard-for-end-users/mobile-client/) for mobile client behaviour.

### You do not have access to the location

Administrators can restrict a location to specific users or groups.

If other users can see the location but you cannot, contact your administrator.

### The location is a service location

[Service locations](../../../features/service-locations.md) are never shown in the Defguard Client. They are established and maintained by a background service, so there is no entry in the location list and nothing for the user to connect to. An **Always on** service location is connected even when the Client is closed.

**Fix:** nothing to fix in the Client. To confirm the connection is up, check the network adapters on the machine or the client status in the Defguard Core web interface - see [Checking if connection is established](../../../features/service-locations.md#checking-if-connection-is-established).

Service locations require Defguard Client for Windows or Linux; the **Pre-logon** mode is Windows-only. If a location was made a service location and the machine runs macOS or a mobile client, it will not be available there at all.
