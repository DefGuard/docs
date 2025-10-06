# External SSO/OpenID providers

{% hint style="warning" %}
This is an enterprise feature. To use it, purchase our [enterprise license](../../enterprise/license.md) or ensure that your deployment does not exceed the [usage limits](../../enterprise/license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

Defguard, [apart from being an identity provider itself](../openid-connect/), supports logging in through external OpenID providers. All providers that support standard and common **code authorization flow should work.**

Here are dedicated tutorials for most common SSO providers:

* [Google](google.md)
* [Microsoft](microsoft.md)
* [Zitadel](zitadel.md)
* [Keycloak](keycloak.md)
* [Okta](okta.md)
* [JumpCloud](jumpcloud.md)
* Tested by our users and confirmed: Authentik, Authelia.

## Prerequisites

In order to configure this feature, the following information is needed to be obtained from a provider of choice:

* Client ID
* Client secret
* (for custom provider) Your provider's base URL
* (for Microsoft as provider) Tenant ID

If you don't know where to find those values, go to the [Examples](./#examples) section, where you will find an example setup for the built-in providers.

### Base URL

The base URL is used to discover all the necessary provider's endpoints which will be used during the authorization flow. Usually, all required information resides at `<PROVIDER_BASE_URL>/.well-known/openid-configuration`. Hence, in order for Defguard to discover the endpoints, you need to provide it with only the base URL value, and the rest (the _well-known_ part) is appended automatically. The base URL should be provided without the trailing slash, some examples:

* `https://accounts.google.com`
* `https:://login.microsoftonline.com/<TENANT_ID>/v2.0`
* `http://<KEYCLOAK_ADDRESS>/realms/<REALM>` (in the case of Keycloak)

### Tenant ID

This is an optional value, required only if you are using Microsoft as your provider. Insert it in the `BASE_URL` field by replacing the `<TENANT_ID>` placeholder.

### Redirect URI

In almost any provider's configuration, you will need to define a set of allowed redirect URIs. Those URIs are the URIs to which the user will be redirected after completing the login on the provider's site. In our case, the user should be redirected back to Defguard, hence, those URIs depend on your Defguard domains and have the following form:

* `<DEFGUARD_DASHBOARD_URL>/auth/callback`
* `<DEFGUARD_ENROLLMENT_URL>/openid/callback`
* `<DEFGUARD_ENROLLMENT_URL>/openid/mfa/callback`

For example, if your Defguard main dashboard is accessible at `https://defguard.my-domain.net` and your users perform the enrollment through a proxy accessible at `https://enrollment.my-domain.net` you would need to enter the following URIs:

* `https://defguard.my-domain.net/auth/callback`
* `https://enrollment.my-domain.net/openid/callback`
* `https://enrollment.my-domain.net/openid/mfa/callback`

These URIs will need to be provided in your provider's configuration. See [#examples](./#examples "mention") to learn more.

## Configuration and setup

In order to configure the external OpenID provider login, go to the settings in the Defguard admin dashboard.

<figure><img src="../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

Everything related to the external OpenID configuration can be found in the OpenID tab of the settings page. The first thing to do here would be to pick your provider using the dropdown menu under the "Provider" label. Next, fill out the required information with values acquired from your provider. If you picked "Microsoft" or "Custom", make sure to also make corresponding changes in the "Base URL" field. After you are done, click "Save changes" to keep your changes.

You may have also noticed the checkbox option on the right. By default, when a new user (i.e. a user of whom Defguard has no record) logs in for the first time using the external OpenID feature, its account is created automatically, based on the personal details (first name, last name, email) received from the external provider. If you'd like to manually manage such users, uncheck the checkbox. Now, users will need to be manually created in Defguard first in order to log in through the external provider.

### OpenID enrollment

When you configure your provider, the proxy will automatically allow enrolling users through it. See [with-external-sso-google-microsoft-custom.md](../../using-defguard-for-end-users/enrollment/with-external-sso-google-microsoft-custom.md "mention") for the process from the user's point of view.

For this to work, make sure you have the following two things set:

* Additional allowed redirect URI in your provider's configuration (see [#redirect-url](./#redirect-url "mention"))
* A `DEFGUARD_PROXY_URL` environment variable set correctly for your proxy (not core). This variable needs to be set for your proxy and should be equal to the URL where users perform the enrollment process. This should be set automatically if you are using the one-line deployment script version `1.2.1` or above. E.g. if your enrollment URL is `https://enrollment.my-domain.net`, set `DEFGUARD_PROXY_URL` to `https://enrollment.my-domain.net`.

#### Disabling automatic account creation

<figure><img src="../../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

If you disable the option above, new users won't be able to automatically go through the enrollment. You will need to create their accounts by hand (with the same email address as the one they have set on your OIDC provider's side) and only then they will have an option to activate it by logging through the provider.

### Directory synchronization

{% hint style="info" %}
This feature is available only in Defguard v1.2.0 and above
{% endhint %}

Defguard supports synchronizing users' and groups' states based on the state of the external provider directory. The following things can be synchronized:

* **User Groups**: Automatically create and assign user groups in Defguard to reflect them in Google Workspace.
* **User Deletion**: Removing a user from the provider's directory can also remove them from Defguard.
* **User Status**: Disabling users in the provider's directory will disable them in Defguard.

Defguard doesn't automatically create users based on the users in your provider's directory. They will have to manually log in to Defguard through your provider for their Defguard accounts to be created. Defguard is responsible only for synchronizing their later state.

#### General configuration

The menu can be found in Defguard settings by navigating to the "OpenID" tab.

<figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

The following configuration options are currently available in the directory synchronization menu for all providers:

* **Synchronize (All/User/Group):** What to synchronize.
  * **All** - synchronize both user state (disabled/enabled), their deletion, and groups
  * **User** - synchronize only user state (disabled/enabled) and whether they've been deleted
  * **Group** - synchronize only user groups
* **Synchronization interval (600s by default):** How often to synchronize with your provider. Very low values may cause issues with the provider API. The user state is also synchronized on login.

{% hint style="danger" %}
If you want to delete your users based on the state of your provider we recommend trying out the "disable" behavior first to make sure everything works as expected. Always back up your database regularly.
{% endhint %}

* **User behaviour (Keep, Disable, Delete):** What to do with Defguard users who are absent from your provider's directory.
* **Admin behaviour (Keep, Disable, Delete):** What to do with Defguard users with admin status (in Defguard) who are absent from your provider's directory.

#### Currently supported providers

* [Google](google.md#directory-synchronization)
* [Microsoft](microsoft.md)
* [Okta](okta.md#directory-synchronization)
* [JumpCloud](jumpcloud.md#directory-synchronization)

## Known issues

### Email conflict between user accounts

In scenario where:

* User1 exists in Defguard
* User2 has email: User1@domain

User2 will not be able to authenticate using OpenID.

This is not a system error, but an administrator’s negligence. The login (as well as the part of the company email corresponding to the login) must be unique, and the administrator is responsible for maintaining user data.

### JumpCloud

When setting up JumpCloud you can encounter an error when attempting to log in with a message `Failed to parse payload JSON: Error(\\\"invalid type: string...`. This is because JumpCloud is returning a token that doesn't conform fully to the OpenID standard. You can try working around this issue by removing the `email_verified` field in your SSO application configuration in JumpCloud. In order to do this, edit your SSO Application and **deselect** the email scope:

<figure><img src="../../.gitbook/assets/obraz (14).png" alt=""><figcaption></figcaption></figure>

Then, add the email below by hand:

<figure><img src="../../.gitbook/assets/obraz (15).png" alt=""><figcaption></figcaption></figure>

Double check that the `email_verified` field is gone from the constant attributes section. The issue should be gone now.
