# Portainer

## Add Portainer app in Defguard

First, go to the defguard OpenID tab and click add new app button.

1. Add the name `Portainer`
2. Redirect Url add `https://yourportainer.com` where yourpotainer.com is the address of your portainer instance.
3. Select the below scopes

* OpenID
* Profile
* Email Then add your app. After successfully adding your app you can see it in the OpenID apps list. When you click on it you will be redirected to the client details page. From this page copy Client ID and Client secret values for later.

## Portainer configuration

When you login to portainer go to **Settings -> Authentication**

On this page select: Authentication method: OAuth

**Provider**

Select **Custom**

**OAuth Configuration**

* **Client ID** -> Client ID from defguard available on client details page.
* **Client secret** -> Client secret from defguard available on client details page.
* **Authorization URL** -> https://\<YOUR\_DEFGUARD\_INSTANCE>/api/v1/oauth/authorize
* **Access token URL** -> https://\<YOUR\_DEFGUARD\_INSTANCE>/api/v1/oauth/token
* **Resource URL** -> https://\<YOUR\_DEFGUARD\_INSTANCE>/api/v1/oauth/userinfo
* **Redirect URL** -> https://\<YOUR\_PORTAINER\_URL>
* **User identifier** -> sub
* **Scopes** -> `openid email profile` **Note** must be spaces separated as in this example
