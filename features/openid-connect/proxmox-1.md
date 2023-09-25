# Matrix / Synapse

{% hint style="warning" %}
For Synapse OIDC to work you'll have to run Defguard with [RSA signing key](../setting-up-your-instance/docker-compose.md#openid-rsa-setup).
{% endhint %}

## Add Synapse app to Defguard

First, go to the Defguard OpenID tab and click add new app button.

1. Add the name `Matrix`
2. Add your matrix address as redirect URL (e.g. `https://matrix.com/_synapse/client/oidc/callback`)
3. Select scopes:

* OpenID
* Profile
* Email
* Phone

4. Submit the form.

After successfully adding your app you can see it in the OpenID apps list. When you click on it you will be redirected to the client details page. From this page copy Client ID and Client secret values for later.

## Matrix configuration

1. Open your homeserver.yaml configuration file.
2. Paste below configuration with appropriate values

```yaml
# OpenID Connect provider settings
oidc_providers:
  - idp_id: Defguard
    idp_name: "Defguard"
    discover: false
    issuer: "https://yourdefguard.com"
    client_id: "CLIENT_ID_FROM_DEFGUARD"  
    client_secret: "CLIENT_SECRET_FROM_DEFGUARD"  
    scopes: ["openid", "profile", "email", "phone"]
    authorization_endpoint: "https://yourdefguard.com/api/v1/oauth/authorize"
    token_endpoint: "https://yourdefguard.com/api/v1/oauth/token"
    userinfo_endpoint: "https://yourdefguard.com/api/v1/oauth/userinfo"
    jwks_uri: "https://yourdefguard.com/api/v1/oauth/discovery/keys"
    user_mapping_provider:
      config:
        localpart_template: "{{ user.email.split('@')[0] }}"
        display_name_template: "{{ user.first_name }} {{ user.last_name }}"
        email_template: "{{ user.email }}"
```

After logging out of your Matrix client of choice you should now be able to select your new realm and login with Defguard using OpenID Connect.
