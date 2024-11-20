# Google

{% hint style="info" %}
Here is [full Google documentation](https://developers.google.com/identity/openid-connect/openid-connect) about this process.
{% endhint %}

1. The Google OpenID Connect can be configured in the [Google Cloud Console](https://console.cloud.google.com)
2.  If you don't have any project setup already (or you want to create a new one for this purpose), create it by clicking the dropdown menu here:

    <figure><img src="../../../.gitbook/assets/image (31).png" alt="" width="312"><figcaption></figcaption></figure>

    If you already have project, make sure to select it in the above dropdown menu.
3. Now, navigate to [`APIs & Services`](https://console.cloud.google.com/apis)
4. We will focus on the consent screen first, select `OAuth consent screen`
5.  Pick the User Type according to your needs, this example will focus on the internal type

    <figure><img src="../../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>
6. Fill in all required details. Make sure to fill the correct domain. This should be the top domain under which your Defguard dashboard can be accessed, not the subdomain (e.g. `defguard.example.com` -> `example.com`).
7.  On the scopes config screen, click `ADD OR REMOVE SCOPES`, Defguard requires at least the following scopes:

    <figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>
8. Proceed until the end and return to the OAuth consent screen dashboard.
9.  Now, go to [`Credentials`](https://console.cloud.google.com/apis/credentials), click `CREATE CREDENTIALS` and choose `OAuth client ID`

    <figure><img src="../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>
10. On the next screen, fill out all required information:

    <figure><img src="../../../.gitbook/assets/obraz (3).png" alt=""><figcaption></figcaption></figure>

    Make sure to select "Web application" as the application type. The other thing to note here is the redirect URI. It is the URI to which the user will be redirected from the external provider's authorization. This URI is in the form of `<DEFGUARD_DASHBOARD_URL>/auth/callback`. Replace `<DEFGUARD_DASHBOARD_URL>` with the URL under which your dashboard is accessible, e.g. `https://defguard.example.com`. If you'd like to use OpenID enrollment through proxy, make sure to enter an additional URI here in the form of `<DEFGUARD_ENROLLMENT_URL>/openid/callback`.
11. After you proceed further, you will be presented with a popup containing your `Client ID` and `Client Secret`, copy them and paste on the Defguard OpenID configuration page.

    <figure><img src="../../../.gitbook/assets/settings.png" alt=""><figcaption></figcaption></figure>
