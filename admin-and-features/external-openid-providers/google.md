---
hidden: true
---

# Google

{% hint style="info" %}
Here is [full Google documentation](https://developers.google.com/identity/openid-connect/openid-connect) about this process.
{% endhint %}

1. The Google OpenID Connect can be configured in the [Google Cloud Console](https://console.cloud.google.com)
2.  If you don't have any project setup already (or you want to create a new one for this purpose), create it by clicking the dropdown menu here:

    <figure><img src="../../.gitbook/assets/image (31).png" alt="" width="312"><figcaption></figcaption></figure>

    If you already have project, make sure to select it in the above dropdown menu.
3. Now, navigate to [`APIs & Services`](https://console.cloud.google.com/apis)
4. We will focus on the consent screen first, select `OAuth consent screen`
5.  Pick the User Type according to your needs, this example will focus on the internal type

    <figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>
6. Fill in all required details. Make sure to fill the correct domain. This should be the top domain under which your Defguard dashboard can be accessed, not the subdomain (e.g. `defguard.example.com` -> `example.com`).
7.  On the scopes config screen, click `ADD OR REMOVE SCOPES`, Defguard requires at least the following scopes:

    <figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>
8. Proceed until the end and return to the OAuth consent screen dashboard.
9.  Now, go to [`Credentials`](https://console.cloud.google.com/apis/credentials), click `CREATE CREDENTIALS` and choose `OAuth client ID`

    <figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>
10. On the next screen, fill out all required information:

    <figure><img src="../../.gitbook/assets/obraz (3).png" alt=""><figcaption></figcaption></figure>

    Make sure to select "Web application" as the application type. The other thing to note here is the redirect URI. It is the URI to which the user will be redirected from the external provider's authorization. This URI is in the form of `<DEFGUARD_DASHBOARD_URL>/auth/callback`. Replace `<DEFGUARD_DASHBOARD_URL>` with the URL under which your dashboard is accessible, e.g. `https://defguard.example.com`. If you'd like to use OpenID enrollment through proxy, make sure to enter an additional URI here in the form of `<DEFGUARD_ENROLLMENT_URL>/openid/callback`.
11. After you proceed further, you will be presented with a popup containing your `Client ID` and `Client Secret`, copy them and paste on the Defguard OpenID configuration page.

    <figure><img src="../../.gitbook/assets/settings.png" alt=""><figcaption></figcaption></figure>

### Directory synchronization

{% hint style="warning" %}
This feature is currently technically limited to 10000 members or groups. High user or group counts may still trigger your provider API limits even below this threshold. If you have many users (200+), we recommend you test this feature first before you decide to turn on automatic user deletion.
{% endhint %}

{% hint style="info" %}
This feature is available only in Defguard v1.2.0 and above
{% endhint %}

This documentation concerns only the Google directory synchronization. For more general information, see the [general directory synchronization guide](./#directory-synchronization).

#### Directory synchronization configuration menu

The menu can be found in Defguard settings by navigating to the "OpenID" tab.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The following configuration options are currently available in the directory synchronization menu specifically for the Google provider:

* **Admin email:** The email of the Google Workspace admin user on whose behalf Defguard will call the Google API
* **Service account in use:** The email of the Google service account that is currently used

To learn more about the rest of the configuration options, see the [general directory synchronization guide](./#directory-synchronization).

#### Directory synchronization setup

1.  Navigate to [Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts) in the Google Cloud console\


    <figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>


2. Click "Create service account"
3.  Give your service account a descriptive name\


    <figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>


4. Skip step 2 and 3 if you are not sure what to configure there.
5.  Go to your newly created service account and add a new key in the "KEYS" tab.\


    <figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>


6. A JSON file will be downloaded after you click "CREATE". Store it securely as it may grant access to your Google Workspace directory.
7. Next, navigate to the "DETAILS" tab and copy the unique ID of your service account.
8. Open the Advanced settings and under Domain-wide delegation click "View google workspace admin console"
9.  Now in the admin console, navigate to [API controls](https://admin.google.com/u/1/ac/owl)\


    <figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>


10. In the API controls, click "Manage domain wide delegation"
11. On the next screen, add a new API client\


    <figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

    Specify the following scopes for your client:\
    `openid, email, profile, https://www.googleapis.com/auth/admin.directory.customer.readonly, https://www.googleapis.com/auth/admin.directory.group.readonly, https://www.googleapis.com/auth/admin.directory.user.readonly`
12. Navigate to the Defguard settings and upload the JSON file you obtained previously. Make sure to also input the email of the account on which behalf the API calls will be made. This account should have access to users and their groups (e.g. email of your account as an admin).
13. Test if you properly set everything up by clicking the "Test connection" button.
