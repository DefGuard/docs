---
icon: up-right-from-square
---

# External OpenID providers

Defguard, [apart from being an identity provider itself](../../../admin-and-features/features-and-configuration/openid-connect/), supports logging in through external OpenID providers. Currently, there are two built-in providers (Google and Microsoft), but there is also an option to specify a custom one.

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

This is an optional value required only if you are using Microsoft as your provider. Insert it in the `BASE_URL` field by replacing the `<TENANT_ID>` placeholder.

### Redirect URI

In almost any provider's configuration you will need to define a set of allowed redirect URIs. Those URIs are the URIs to which the user will be redirected after completing the login on the provider's site. In our case, the user should be redirected back to Defguard, hence, those URIs depend on your Defguard domains and have the following form:

* `<DEFGUARD_DASHBOARD_URL>/auth/callback`
* `<DEFGUARD_ENROLLMENT_URL>/openid/callback`

For example, if your Defguard main dashboard is accessible at `https://defguard.my-domain.net` and your users perform the enrollment through a proxy accessible at `https://enrollment.my-domain.net` you would need to enter the following URIs:

* `https://defguard.my-domain.net/auth/callback`
* `https://enrollment.my-domain.net/openid/callback`

These URIs will need to be provided in your provider's configuration. See [#examples](./#examples "mention") to learn more.

## Configuration and setup

In order to configure the external OpenID provider login, go to the settings in the Defguard admin dashboard.

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

Everything related to the external OpenID configuration can be found in the OpenID tab of the settings page. First thing to do here would be to pick your provider using the dropdown menu under the "Provider" label. Next, fill out the required information with values acquired from your provider. If you picked "Microsoft" or "Custom", make sure to also make corresponding changes in the "Base URL" field. After you are done, click "Save changes" to keep your changes.

You may have also noticed the checkbox option on the right. By default, when a new user (i.e. a user of whom Defguard has no record) logs in for the first time using the external OpenID feature, its account is created automatically, based on the personal details (first name, last name, email) received from the external provider. If you'd like to manually manage such users, uncheck the checkbox. Now users will need to be manually created in Defguard first in order to log in through the external provider.

### OpenID enrollment

When you configure your provider, the proxy will automatically allow enrolling users through it. See [with-external-sso-google-microsoft-custom.md](../../../help/enrollment/with-external-sso-google-microsoft-custom.md "mention") for the process from the user's point of view.

For this to work, make sure you have the following two things set:

* Additional allowed redirect URI in your provider's configuration (see [#redirect-url](./#redirect-url "mention"))&#x20;
* A `DEFGUARD_PROXY_URL` environment variable set correctly for your proxy (not core). This variable needs to be set for your proxy and should be equal to the URL where users perform the enrollment process. This should be set automatically if you are using the one-line deployment script version `1.2.1` or above. E.g. if your enrollment URL is `https://enrollment.my-domain.net`, set `DEFGUARD_PROXY_URL` to `https://enrollment.my-domain.net`.

#### Disabling automatic account creation

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

If you disable the option above, new users won't be able to automatically go through the enrollment. You will need to create their accounts by hand (with the same email address as the one they have set on your OIDC provider's side) and only then they will have an option to activate it by logging through the provider.

## Examples

* [Google](google.md)
* [Microsoft](microsoft.md)
* [Zitadel](zitadel.md)
* [Keycloak](keycloak.md)

