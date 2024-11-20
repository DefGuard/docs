# Microsoft

1. Go to [https://portal.azure.com/](https://portal.azure.com)
2. Navigate to Microsoft Entra ID
3.  In the Microsoft Entra ID, click Manage and select App registrations from the menu on the left.

    <figure><img src="../../../.gitbook/assets/obraz (4).png" alt=""><figcaption></figcaption></figure>
4. Click "Make new registration"
5.  Fill out the form, like in the example:

    <figure><img src="../../../.gitbook/assets/obraz (5).png" alt=""><figcaption></figcaption></figure>

Make sure the Redirect URL you insert here is correct. Replace `defguard.example.com` with the domain you use for your Defguard dashboard. If you'd like to use OpenID enrollment through proxy, make sure to enter an additional URI here in the form of `<DEFGUARD_ENROLLMENT_URL>/openid/callback`.

6.  You should be now on the registered application's management screen. You can copy the client's ID and the tenant ID from here, as you need to provide them on the Defguard settings' page.

    <figure><img src="../../../.gitbook/assets/Zrzut ekranu 2024-10-18 o 16.13.54.png" alt=""><figcaption></figcaption></figure>
7. Go to Defguard settings, click the OpenID tab and paste the copied client ID. The tenant ID should be inserted instead of the `<TENANT_ID>` placeholder in the base URL field.
8.  Now back in Microsoft Entra ID, still in your newly created application, go to **Certificates & Secrets**

    <figure><img src="../../../.gitbook/assets/obraz (8).png" alt=""><figcaption></figcaption></figure>
9. Click Client secrets and create a new client secret. Copy its **value** and paste it in your Defguard OpenID settings.
10. Go to Token configuration (in the menu on the left) and add a new optional token claim.
11. Make sure to select the ID token type and the following claims:

    <figure><img src="../../../.gitbook/assets/obraz (9).png" alt=""><figcaption></figcaption></figure>
12. Accept the popup or configure the API permissions manually.

<figure><img src="../../../.gitbook/assets/obraz (10).png" alt=""><figcaption></figcaption></figure>

13. Now you should be good to go. A new login button should appear on the login screen.

#### Custom OpenID provider

{% hint style="warning" %}
Defguard supports custom providers that allow a **code** response type in the OpenID authorization flow.
{% endhint %}

You can also configure a custom OpenID provider. The key thing here is setting up the **Base URL** correctly. This URL is used to discover all the endpoints required for the authorization flow.

The easiest way of obtaining the Base URL is finding out what is the OpenID `.well-known` URL of your provider. For example, for Google it's `https://accounts.google.com/.well-known/openid-configuration`, in this case, the Base URL would be `https://accounts.google.com` (note the lack of a trailing slash). The part starting with `/.well-known` is added automatically, so it should be omitted from the Base URL. This is explained in more detail in the [Base URL](microsoft.md#base-url) section.

In order to get the **Client ID** and **Client Secret** values, refer to the documentation of your custom provider of choice.

When configuring your external OpenID provider, at some point you will need to provide a callback URL, which will redirect the user back to Defguard. This URL is in form of `<DEFGUARD_DASHBOARD_URL>/auth/callback`. Replace `<DEFGUARD_DASHBOARD_URL>` with the URL under which your dashboard is accessible, e.g. `https://defguard.example.com`. If you'd like to use OpenID enrollment through proxy too, make sure to enter an additional URI in the form of `<DEFGUARD_ENROLLMENT_URL>/openid/callback`.

If you're having issues with your custom provider's base URL, check Defguard's (core) logs. It should say what URL it expected.&#x20;
