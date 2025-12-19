# Desktop client auto-provisioning

## Overview

The Defguard desktop client supports automated provisioning through a configuration file-based approach that enables it to work across all supported platforms. This feature streamlines client deployment in enterprise environments by automating the initial user enrollment and client configuration process.

## Provisioning process overview

While the provisioning process is dependent on your specific environment and tooling, there are some general steps to you'll probably follow:

1. Generate enrollment tokens for your users. This can be automated by [leveraging our REST API](generating-enrollment-tokens-with-defguard-rest-api.md).
2. Transfer tokens to end-user machines as a [configuration file](./#provisioning-configuration-file).
3. Install Defguard desktop client on end-user machines.
4. On first startup the user will be redirected to the enrollment flow.

### Windows platform

We provide additional tooling and a [detailed guide](auto-provisioning-in-windows-environments/) for an end-to-end provisioning process on Windows platforms with on-premise Active Directory or Entra ID.

## Client-Side Implementation Details

Automated provisioning is enabled by a desktop client functionality which reads an optional configuration file and redirects the end user to the enrollment flow on first startup.

### Initialization Check

On startup, the desktop client performs an initialization check to determine if it has been previously configured. A client is considered initialized if at least one VPN instance has been configured.

### Provisioning Configuration File

If the client has not been initialized, it searches for a provisioning configuration file in the default application data directory (the same [location where the client database file resides](../../using-defguard-for-end-users/desktop-client/#storage)).

**File location:** `<app_data_directory>/provisioning.json`

**File format:** The configuration file is a JSON document containing two required fields:

* `enrollment_token` - The enrollment token for the user
* `enrollment_url` - The URL of the Defguard instance

**Example file content:**

```json
{
  "enrollment_token": "6B8fg2wiyczb1uDCDwUyMsnYz6qQBVu0",
  "enrollment_url": "https://proxy.example.com/"
}
```

These values are analogous to those used in the standard user enrollment process.

### Client Provisioning Process

1. If a `provisioning.json` file is found, the client reads the configurationThe client attempts to configure itself using the provided enrollment token
2. If the user has not yet completed enrollment, they are redirected to the enrollment screen
3. The user follows the standard enrollment process to finish setting up their account
4. Once enrollment is complete, the user can connect to VPN locations and access protected resources
