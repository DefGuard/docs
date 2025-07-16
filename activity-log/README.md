# Activity & Audit logs

{% hint style="info" %}
This feature is available starting from version 1.4
{% endhint %}

The Activity Log provides a comprehensive view of user interactions within your Defguard instance. This allows you to monitor user behavior, troubleshoot issues, and maintain an audit trail of important activities.

## Viewing Activity log events

Activity log is available as a dedicated page in Defguard core Web UI that's used to manage your instance.

To access it click the `Activity log` button in the navbar.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>Activity log page</p></figcaption></figure>

### Overview

Activity log page displays a chronological list of user-initiated events. By default most recent events are on top.

Each entry in the list contains following fields:

* **Date** - timestamp of when an event has occurred
* **User** - which user triggered the event
* **IP** - location from which the action was performed
* **Event** - brief description of the event
* **Module** - which module given event belongs to
* **Device** - device (or more specifically user agent) from which the action was performed

### Modules

Events are grouped into modules based on the part of the system they are related to.

Currently there are four modules:

* **Defguard** - operations performed in the core Web UI (e.g. adding users, modifying devices, managing groups etc.)&#x20;
* **Client** - actions performed by desktop client applications
* **Enrollment** - events related to the [user enrollment](../help/enrollment/) process
* **VPN -** events related to VPN clients (e.g. client connecting to a location)

### Filtering

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Event filter modal</p></figcaption></figure>

By clicking the `Filter` button above the list you can narrow down the displayed events based on following criteria:&#x20;

* Event
* Module
* Users

For each of those you can select multiple options.

Filtering by date can be done by clicking the `Time range` button above the list.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Time range filter modal</p></figcaption></figure>

### Sorting

By default the Activity log is sorted in reverse chronological order (most recent event on top).&#x20;

To change the order you can click on the header of the `Date` column.

### Search

You can also use the `Search` input above the list to look for specific events.

You can search by:

* **Username**
* **Module**
* **Event**
* **Device**

The search is case-insensitive and will match partial text.

Note that filtering & searching are composable operations, so if you've already applied some filters the search will be performed only among those filtered events.

## Permissions

Access to the Activity log is controlled by user permissions.

Each user can always view their own activities (events triggered by themselves).

Additionally administrators can view events related to all users.

## Events tracked in Activity Log

At the moment following events are tracked in the Activity log:

* **Defguard** module
  * UserLogin
  * UserLoginFailed
  * UserLogout
  * UserMfaLogin
  * UserMfaLoginFailed
  * RecoveryCodeUsed
  * PasswordChangedByAdmin
  * PasswordChanged
  * PasswordReset
  * MfaDisabled
  * UserMfaDisabled
  * MfaTotpDisabled
  * MfaTotpEnabled
  * MfaEmailDisabled
  * MfaEmailEnabled
  * MfaSecurityKeyAdded
  * MfaSecurityKeyRemoved
  * UserAdded
  * UserRemoved
  * UserModified
  * UserGroupsModified
  * UserDeviceAdded
  * UserDeviceRemoved
  * UserDeviceModified
  * NetworkDeviceAdded
  * NetworkDeviceRemoved
  * NetworkDeviceModified
  * ActivityLogStreamCreated
  * ActivityLogStreamModified
  * ActivityLogStreamRemoved
  * VpnLocationAdded
  * VpnLocationRemoved
  * VpnLocationModified
  * ApiTokenAdded
  * ApiTokenRemoved
  * ApiTokenRenamed
  * OpenIdAppAdded
  * OpenIdAppRemoved
  * OpenIdAppModified
  * OpenIdAppStateChanged
  * OpenIdProviderModified
  * OpenIdProviderRemoved
  * SettingsUpdated
  * SettingsUpdatedPartial
  * SettingsDefaultBrandingRestored
  * GroupsBulkAssigned
  * GroupAdded
  * GroupModified
  * GroupRemoved
  * GroupMemberAdded
  * GroupMemberRemoved
  * GroupMembersModified
  * WebHookAdded
  * WebHookModified
  * WebHookRemoved
  * WebHookStateChanged
  * AuthenticationKeyAdded
  * AuthenticationKeyRemoved
  * AuthenticationKeyRenamed
  * ClientConfigurationTokenAdded
  * UserSnatBindingAdded
  * UserSnatBindingRemoved
  * UserSnatBindingModified
* **Enrollment** module
  * EnrollmentStarted
  * EnrollmentDeviceAdded
  * EnrollmentCompleted
  * PasswordResetRequested
  * PasswordResetStarted
  * PasswordResetCompleted
  * TokenAdded
* **VPN** module
  * ConnectedToMfaLocation
  * DisconnectedFromMfaLocation
  * MfaFailed
  * ConnectedToLocation
  * DisconnectedFromLocation

## Streaming to external SIEM systems

Please note, that enterprise version supports streaming of audit logs to e[xternal SIEM systems. More on this topic in dedicated documentation section](activity-log-streaming/).

