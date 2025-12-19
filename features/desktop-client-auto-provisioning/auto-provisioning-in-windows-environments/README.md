# Auto-provisioning in Windows environments

## Windows Auto-Provisioning

At the moment the complete auto-provisioning process is officially supported on the Windows platform and handles both on-premise Active Directory as well as Entra ID environments.

### MSI Installer Integration

The official Defguard Windows [MSI installer](https://defguard.net/download/) includes a PowerShell [helper script](https://github.com/DefGuard/client/blob/2a348dc4e07c2cdf6284af998dc092c65dd7d2dc/src-tauri/resources-windows/scripts/Get-ProvisioningConfig.ps1) that enables automatic provisioning during installation.

This script executes when the installer is run with the `PROVISIONING=1` argument provided.

**Example installation command:**

```powershell
msiexec /i defguard-client.msi PROVISIONING=1
```

#### Domain Detection and Configuration Retrieval

The provisioning script automatically detects whether the machine is joined to:

* **On-premise Active Directory (AD)**
* **Microsoft Entra ID** (formerly Azure AD)

If the machine is domain-joined, the script attempts to retrieve provisioning configuration from the appropriate directory service.

If the machine is not joined to any domain the script exits gracefully.

For hybrid-joined machines (joined to both Entra and AD) the script proceeds with executing the **Active Directory** flow.

#### **Active Directory Configuration**

For Active Directory environments, the provisioning configuration is expected to be stored as JSON in a single user attribute. The attribute name can be specified as an optional argument to the installer.

**Script argument name**: `ADAttribute`

**Default attribute name:** `defguardProvisioningConfig`

**Example attribute value:** `{"enrollmentUrl":"https://proxy.example.com/","enrollmentToken":"DuK8xRes5zYqem0fcAViQPRvIrNkGz7S"}`

**Example installation command:**

```powershell
msiexec /i defguard-client.msi PROVISIONING=1 ADAttribute="extensionAttribute4"
```

#### **Entra ID Configuration**

For Entra ID environments, provisioning data is retrieved from custom security attributes within a dedicated attribute group.

**Attribute group name:** `Defguard`

**Required attributes:**

* `EnrollmentToken` - The user's enrollment token
* `EnrollmentUrl` - The Defguard instance URL

#### Configuration File Creation

After successfully retrieving provisioning data from AD or Entra ID, the script creates a `provisioning.json` file in the expected application data directory, making it available for the client to use on first launch.

### Generalized Windows Auto-Provisioning Workflow

This section gives a high-level overview of the provisioning workflow. For more specific instructions for specific Windows environments see dedicated guides:

* [On-premise Active Directory environments](on-premise-active-directory-environments.md)

#### Provisioning Steps

1. **User Management**
   * Add users to the Defguard instance using one of the following methods:
     * Two-way LDAP synchronization (for Active Directory)
     * OpenID directory synchronization (for Entra ID)
2. **Token Generation**
   * Generate enrollment tokens for users
   * This can be automated using the Defguard REST API
   * API access requires an [authorization token](../../integrations/api-tokens.md#generating-api-token)
3. **Client Installation**
   * Install the defguard-client application on user machines using the MSI installer
   * Pass the `PROVISIONING=1` argument to enable automatic provisioning
   * Example command: `msiexec /i defguard-client.msi PROVISIONING=1 ADAttribute="description"`
4. **Automatic Configuration**
   * During installation, the bundled script fetches provisioning configuration from Active Directory or Entra ID
   * The configuration is written to the client's [data directory](../../../using-defguard-for-end-users/desktop-client/#storage) as explained [above](./#configuration-file-creation)
5. **User Enrollment**
   * When the user launches the client for the first time, they are guided through the enrollment process
   * The enrollment uses the pre-configured token and URL from the provisioning file
6. **Client Ready**
   * Once enrollment is complete, the user can establish VPN connections and access protected resources
