---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/external-openid-providers/microsoft
---

# Microsoft

Firstly, we need to obtain credentials such as&#x20;

* `Tenant ID`
* `Client ID`
* `Client secret`

If you already have them, please skip to [#configuring-microsoft-as-external-oidc-in-defguard](microsoft.md#configuring-microsoft-as-external-oidc-in-defguard "mention")

## Obtaining basic credentials

1. Go to [https://portal.azure.com/](https://portal.azure.com)
2. Navigate to Microsoft Entra ID
3.  In the Microsoft Entra ID, click Manage and select App registrations from the menu on the left.

    <figure><img src="../../.gitbook/assets/obraz (9).png" alt=""><figcaption></figcaption></figure>
4. Click "Make new registration"
5.  Fill out the form, like in the example:

    <figure><img src="../../.gitbook/assets/obraz (10).png" alt=""><figcaption></figcaption></figure>

Make sure the Redirect URL you insert here is correct. Replace `defguard.example.com` with the domain you use for your Defguard dashboard. If you'd like to use OpenID enrollment through proxy, make sure to enter an additional URI here in the form of `<DEFGUARD_PUBLIC_URL>/openid/callback`.

6.  You should be now on the registered application's management screen. You can copy the client's ID and the tenant ID from here, as you need to provide them on the Defguard settings' page.

    <figure><img src="../../.gitbook/assets/Zrzut ekranu 2024-10-18 o 16.13.54.png" alt=""><figcaption></figcaption></figure>
7.  Now back in Microsoft Entra ID, still in your newly created application, go to **Certificates & Secrets**

    <figure><img src="../../.gitbook/assets/obraz (13).png" alt=""><figcaption></figcaption></figure>
8. Go to `Certificates & secrets` and create a new client secret. Copy its **value.**

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

9. Go to Token configuration (in the menu on the left) and add a new optional token claim.
10. Make sure to select the ID token type and the following claims:

    <figure><img src="../../.gitbook/assets/obraz (14).png" alt=""><figcaption></figcaption></figure>
11. Accept the popup or configure the API permissions manually.

<figure><img src="../../.gitbook/assets/obraz (15).png" alt=""><figcaption></figcaption></figure>

12. Now you should be good to go.

## Obtaining Directory Synchronization credentials

{% hint style="info" %}
This feature is available only in Defguard 1.2.1 and above
{% endhint %}

{% hint style="warning" %}
This feature is currently technically limited to 10000 members or groups. High user or group counts may still trigger your provider API limits even below this threshold. If you have many users (200+), we recommend you test this feature first before you decide to turn on automatic user deletion.
{% endhint %}

Defguard supports synchronizing groups' and users' states based on your Microsoft directory.

Make sure to check the [general guide to directory synchronization](./#directory-synchronization) to learn more about the available configuration options.

#### Setup

1. Go back to your app registrations in Microsoft Entra ID and select the app you registered during the provider setup.
2.  Navigate to API permissions

    <figure><img src="../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>
3.  Click "Add a permission", then select "Microsoft Graph"

    <figure><img src="../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>
4.  Select "Application permissions", as Defguard will perform the synchronization in the background.

    <figure><img src="../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>
5. Assign the following permissions:
   * `GroupMember.Read.All`
   * `Group.Read.All`
   * `User.Read.All`
6.  Now grant admin consent for the permissions using the "Grant admin consent for" button\\

    <figure><img src="../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>
7. You should be good to go now. Navigate to the directory sync settings in Defguard and try to test your setup using the test connection button.





## Configuring Microsoft as external OIDC in Defguard

1. Go to **Settings** > **External identity providers.**

<figure><img src="../../.gitbook/assets/Screenshot 2026-04-24 at 11.22.31.png" alt=""><figcaption></figcaption></figure>

2. Click "**Connect"** in row with **Microsoft**.

<figure><img src="../../.gitbook/assets/base copy 2.png" alt=""><figcaption></figcaption></figure>

3. Fill out **Tenant ID**, **Client ID**, **Client secret** (Check [#obtaining-basic-credentials](microsoft.md#obtaining-basic-credentials "mention"))

<figure><img src="../../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>

4. Click "**Continue**"
5. If you decide to use **Directory Synchronization**, enable it. (If you don't want **Directory Synchronization**, skip to step 6.

<figure><img src="../../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
In order for Entra users to be created in Defguard they must have the following attributes set:

* email (or otherMails)
* first name
* last name
{% endhint %}

By default, directory sync guaranteed only that the state and group membership of Defguard users also present in the directory is up-to-date. It does not create Defguard users until they log in using an External OpenID provider.

To create Defguard users during directory sync enable the `Prefetch users` option:

<figure><img src="../../.gitbook/assets/Screenshot 2026-04-24 at 12.01.15.png" alt=""><figcaption></figcaption></figure>

6. Click "**Continue**"

<figure><img src="../../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

If no errors occurred during configuration, you will see a message indicating that your OIDC provider has been successfully added.

7. Click "**Finish**"
