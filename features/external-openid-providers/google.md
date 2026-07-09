---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/external-openid-providers/google
---

# Google

Firstly, we need to obtain credentials such as

* `Client ID`
* `Client secret`
* `Admin email` (optionally)
* `Service account key` (optionally)

If you already have them, please skip to [#configuring-google-as-external-oidc-in-defguard](google.md#configuring-google-as-external-oidc-in-defguard "mention")

## Obtaining basic credentials

{% hint style="info" %}
Here is [full Google documentation](https://developers.google.com/identity/openid-connect/openid-connect) about this process.
{% endhint %}

1. The Google OpenID Connect can be configured in the [Google Cloud Console](https://console.cloud.google.com)
2.  If you don't have any project setup already (or you want to create a new one for this purpose), create it by clicking the dropdown menu here:

    <figure><img src="../../.gitbook/assets/image (141).png" alt="" width="312"><figcaption></figcaption></figure>

    If you already have project, make sure to select it in the above dropdown menu.
3. Now, navigate to [`APIs & Services`](https://console.cloud.google.com/apis)
4. We will focus on the consent screen first, select `OAuth consent screen`
5.  Pick the User Type according to your needs, this example will focus on the internal type

    <figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>
6. Fill in all required details. Make sure to fill the correct domain. This should be the top domain under which your Defguard dashboard can be accessed, not the subdomain (e.g. `defguard.example.com` -> `example.com`)
7.  On the scopes config screen, click `ADD OR REMOVE SCOPES`, Defguard requires at least the following scopes:

    <figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>
8. Proceed until the end and return to the OAuth consent screen dashboard.
9.  Now, go to [`Credentials`](https://console.cloud.google.com/apis/credentials), click `CREATE CREDENTIALS` and choose `OAuth client ID`

    <figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>
10. On the next screen, fill out all required information:

    <figure><img src="../../.gitbook/assets/obraz (8).png" alt=""><figcaption></figcaption></figure>

    Make sure to select "Web application" as the application type. The other thing to note here is the redirect URI. It is the URI to which the user will be redirected from the external provider's authorization. This URI is in the form of `<DEFGUARD_URL>/auth/callback`. Replace `<DEFGUARD_URL>` with the URL under which your dashboard is accessible, e.g., `https://defguard.example.com`. If you'd like to use OpenID enrollment through proxy (and MFA, make sure to enter an additional URIs here in the form of `<DEFGUARD_PUBLIC_URL>/openid/callback`, and if you use External MFA please add also: `<DEFGUARD_PUBLIC_URL>/openid/mfa/callback`
11. After you proceed further, you will be presented with a popup containing your `Client ID` and `Client Secret`, copy them as you will need them in Defguard

## Obtaining Directory Synchronization credentials

1.  Navigate to [Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts) in the Google Cloud console

    <figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>
2. Click "Create service account"
3.  Give your service account a descriptive name

    <figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>
4. Skip step 2 and 3 if you are not sure what to configure there
5.  Go to your newly created service account and add a new key in the "KEYS" tab

    <figure><img src="../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>
6. A JSON file will be downloaded after you click "CREATE". Store it securely as it may grant access to your Google Workspace directory
7. Next, navigate to the "DETAILS" tab and copy the unique ID of your service account. It will be needed later.
8. Open the Advanced settings and under Domain-wide delegation click "View Google Workspace admin console"
9.  Now in the admin console, navigate to [API controls](https://admin.google.com/u/1/ac/owl)

    <figure><img src="../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>
10. In the API controls, click "Manage domain wide delegation"
11. On the next screen, add a new API client

    <figure><img src="../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

    In the `Client ID` field paste `unique ID` from step 7.\
    \
    Specify the following scopes for your client:\
    `openid, email, profile, https://www.googleapis.com/auth/admin.directory.customer.readonly, https://www.googleapis.com/auth/admin.directory.group.readonly, https://www.googleapis.com/auth/admin.directory.user.readonly`
12. Navigate to the Defguard settings and upload the **JSON** file you obtained previously. Make sure to also input the email of the account on which behalf the API calls will be made. This account should have access to users and their groups (e.g., email of your account as an admin)

## Configuring Google as external OIDC in Defguard

1. Go to **Settings → External identity providers**

<figure><img src="../../.gitbook/assets/Screenshot 2026-04-24 at 11.22.31.png" alt=""><figcaption></figcaption></figure>

2. Click "**Connect"** in row with **Google**

<figure><img src="../../.gitbook/assets/base copy.png" alt=""><figcaption></figcaption></figure>

3. Fill **Client ID** and **Client secret**. (See [#obtaining-basic-credentials](google.md#obtaining-basic-credentials "mention"))

<figure><img src="../../.gitbook/assets/image (289).png" alt=""><figcaption></figcaption></figure>

4. Click "**Continue**"
5. If you decide to use **Directory Synchronization**, enable it and fill out "**Admin email"** and upload **Service account key** file. (See [#obtaining-directory-synchronization-credentials](google.md#obtaining-directory-synchronization-credentials "mention"))

<figure><img src="../../.gitbook/assets/image (290).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
This feature is currently technically limited to 10000 members or groups. High user or group counts may still trigger your provider API limits even below this threshold. If you have many users (200+), we recommend you test this feature first before you decide to turn on automatic user deletion.
{% endhint %}

* **Admin email:** The email of the Google Workspace admin user on whose behalf Defguard will call the Google API
* **Service Account Key:** JSON file used to authenticate your application with Google APIs using a service account

6. Click "**Continue"**

<figure><img src="../../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

If no errors occurred during configuration, you will see a message indicating that your OIDC provider has been successfully added

7. Click "**Finish**"
