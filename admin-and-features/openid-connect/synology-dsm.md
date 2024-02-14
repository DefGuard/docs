# Synology DSM

{% hint style="warning" %}
For Synology SSO Client to work with defguard you have to run your instance with [RSA signing key](../setting-up-your-instance/docker-compose.md#openid-rsa-setup).
{% endhint %}

{% hint style="warning" %}
It's important to note that this integration does not create user accounts. To authorize with defguard you have to create Synology users with matching usernames.
{% endhint %}

## Add Synology app in defguard

* in defguard web UI go to OpenID Apps and click `Add new` button
* fill in the form
  * **App name**: Synology (or whatever you prefer)
  * **Redirect URL 1**: URL of your Synology NAS web interface (DSM), for example `https://dsm.example.com`
  * **Scopes**: select `OpenID` and `Profile`
* submit the form

After successfully adding your app you can see it in the OpenID apps list. When you click on it you will be redirected to the client details page. From this page copy Client ID and Client secret values for later.

## Configure SSO Client in Synology DSM

* log into your Synology NAS web UI using an account with admin permissions
* got to **Control Panel** > **Domain/LDAP** > **SSO Client**
*   check `Enable OpenID Connect SSO service` and open SSO Settings\


    <figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
* fill in and save the settings form
  * **Profile**: OIDC
  * **Account type**: Domain/LDAP/local
  * **Name**: defguard (or whatever you prefer, this will be used on login screen)
  * **Well-known URL**: `<your_defguard_url>/.well-known/openid-configuration`, for example `https://id.example.com/.well-known/openid-configuration`
  * **Application ID**: copied from client app details in defguard
  * **Application secret**: copied from client app details in defguard
  * **Redirect URI**: address of your Synology NAS, for example https://dsm.example.com
  * **Authorization scope**: `openid profile`
  *   **Username claim**: `preferred_username`\


      <figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

After this a new tab should be added to the DSM login form:

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

You can also make defguard the default login option in SSO client settings.
