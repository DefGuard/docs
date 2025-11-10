# Entra ID environments

## Overview

This guide desctibes an example desktop client provisioning scenario in an Entra ID environment.

The guide assumes that our goal is to provision desktop clients for a set of users in a specific Entra group.

Please note that it is just a reference, you should adjust it to work with your specific environment and preferred tooling.

## Generating enrollment tokens

To automate the process as much as possible we've prepared an example PowerShell script which can be used to generate enrollment tokens for all users in a specified group.&#x20;

The script can be downloaded [here](https://github.com/DefGuard/client/blob/a7ced1d0e3e3831c553a291126304475777d9176/src-tauri/resources-windows/admin-scripts/GenerateEnrollmentTokensEntraID.ps1).

The assumption is that this script will be run by an Entra administrator on a domain-joined machine.

### Prerequisites

* network access to a Defguard instance
* Defguard user account with admin privileges&#x20;
* domain-joined machine
* Entra user account with `User Administrator` and `Attribute Assignment Administrator` roles
* custom security attribute set (default expected name is `Defguard`, but a different one can be used) with following attributes defined in Entra:
  * `EnrollmentUrl` (String)
  * `EnrollmentToken` (String)

### Defguard authentication

To securely access your Defguard instance's REST API you need to generate an [API Token](../integrations/api-tokens.md#generating-api-token).

### Entra authentication

By default the script will use current user's credentials for Entra authentication.&#x20;

It will present an interactive propmt for selecting a user account. If this fails the script will attempt the device code auth flow as a fallback.

### Required parameters

* `Url` - URL of your Defguard instance
* `ApiToken` - your API token for Defguard API access
* `GroupName` - name of user group for which to generate enrollment tokens

### Optional parameters

* `AttributeSetName` - name of custom security attribute set where the provisioning config data will be stored

### Example script execution command

`.\GenerateEnrollmentTokensEntraID.ps1 -Url "https://defguard.example.com" -ApiToken "dg-your-generated-token" -GroupName "DgProvisioning"`

## Client provisioning scenario

1. **Setup target users**
   * prepare a user group containing all the users you intend to perform client provisioning for
2. **User synchronization**
   * Configure OpenID directory synchronization as described [here](../external-openid-providers/microsoft.md)
   * **IMPORTANT**: enable the `Prefetch users` option to create directory users in Defguard
3. **Token Generation**
   * Generate enrollment tokens for users using the [helper script](entra-id-environments.md#generating-enrollment-tokens)
4. **Client Installation**
   * Install the `defguard-client` application on user machines using the [MSI installer](./#msi-installer-integration)
   * Pass the `PROVISIONING=1` argument to execute provisioning script during installation
   * Example command: `msiexec /i defguard-client.msi PROVISIONING=1 ADAttribute="description"`
5. **Automatic Configuration**
   * During installation, the bundled script fetches [provisioning configuration](./#active-directory-configuration) from Entra ID
   * The configuration is written to the client's [data directory](../../using-defguard-for-end-users/desktop-client/#storage) as explained [here](./#configuration-file-creation)
6. **User Enrollment**
   * When the user launches the client for the first time, they are guided through the enrollment process
   * The enrollment uses the pre-configured token and URL from the provisioning file
7. **Client Ready**
   * Once enrollment is complete, the user can establish VPN connections and access protected resources
