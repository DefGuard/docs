# Microsoft certificate authentication

{% hint style="warning" %}
**Availability**

This feature is available in Business and Enterprise plans, starting with Defguard 2.2.1. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

Defguard can sign in to Microsoft Entra ID with a certificate instead of a client secret. Use this page to set it up for a new Microsoft provider, switch an existing provider to it, and replace the certificate before it expires.

## Overview

When users sign in with Microsoft, and when [Directory synchronization](../#directory-synchronization) runs, Defguard talks to Microsoft on behalf of your app registration. Microsoft must first check that the request really comes from Defguard. Defguard can prove this in two ways:

* **Client secret.** Microsoft generates a long password. You copy it into Defguard, and Defguard sends it to Microsoft with each request.
* **Certificate.** You create a key pair: a **certificate** (the public part) and a **private key** (the secret part). You upload the certificate to Microsoft and both files to Defguard. Defguard uses the private key to sign a short proof (valid for 5 minutes) for each request. Microsoft checks the signature with the certificate. The private key is never sent to Microsoft.

|                                       | Client secret                    | Certificate                                                   |
| ------------------------------------- | -------------------------------- | ------------------------------------------------------------- |
| What Microsoft stores                 | The secret itself                | Only the public certificate                                   |
| What travels over the network         | The secret, with every request   | A signed proof that is valid for 5 minutes                    |
| Who creates it                        | Microsoft                        | You, or your company's certificate authority                  |
| Expiry warnings in Defguard           | No                               | Yes, the expiry date is shown and admins get email reminders  |

Choose the certificate when your security policy does not allow shared secrets for applications, or when you want Defguard to warn you before the credential expires.

{% hint style="info" %}
Certificate authentication is available only for the **Microsoft** provider. It is used both for user sign-in and for Directory synchronization.
{% endhint %}

## Before you start

You need:

* An app registration in Microsoft Entra ID with the **Tenant ID**, **Client ID**, redirect URL and token claims set up. Follow [Obtaining basic credentials](./#obtaining-basic-credentials), but skip the step that creates a client secret.
* Permission to change the app registration in Microsoft Entra ID.
* An admin account in Defguard.
* To receive expiry reminders: [email notifications (SMTP)](../../notifications/setting-up-smtp-for-email-notifications.md) set up in Defguard.

## Step 1: Create a certificate and a private key

Defguard accepts a certificate and private key that meet all of these requirements:

* The key is an **RSA** key. Other key types, for example EC (elliptic curve) keys, are rejected.
* Both files are in **PEM** format. This is a text format. The certificate file starts with `-----BEGIN CERTIFICATE-----`. The key file starts with `-----BEGIN PRIVATE KEY-----` or `-----BEGIN RSA PRIVATE KEY-----`.
* The private key is not protected with a password.
* The certificate is valid now: it is not expired, and its start date is not in the future.

A self-signed certificate is enough, because Microsoft only compares it with the copy that you upload. If your company has its own certificate authority, you can use a certificate from it instead.

To create a self-signed certificate that is valid for one year, run this command on Linux or macOS. On Windows, run it in Git Bash or WSL.

```bash
openssl req -x509 -newkey rsa:2048 -nodes -keyout defguard-entra.key -out defguard-entra.crt -days 365 -subj "/CN=defguard-entra"
```

The command creates two files in the current folder:

| File                  | What it is      | Where it goes                                  |
| --------------------- | --------------- | ---------------------------------------------- |
| `defguard-entra.crt`  | The certificate | Microsoft Entra ID **and** Defguard            |
| `defguard-entra.key`  | The private key | **Only** Defguard. Do not share or upload it anywhere else. |

{% hint style="warning" %}
Anyone who has the private key can sign in to Microsoft as your Defguard app. Delete local copies of `defguard-entra.key` after you upload it to Defguard, or keep it in a password manager or secrets vault.
{% endhint %}

### If you have a PFX or P12 file

Windows and many certificate authorities give you one `.pfx` (or `.p12`) file that contains both the certificate and the key. Convert it to the two PEM files that Defguard needs. OpenSSL asks for the password of the `.pfx` file.

```bash
openssl pkcs12 -in certificate.pfx -clcerts -nokeys | openssl x509 -out defguard-entra.crt
```

```bash
openssl pkcs12 -in certificate.pfx -nocerts -nodes | openssl pkey -out defguard-entra.key
```

## Step 2: Upload the certificate to Microsoft Entra ID

1. Go to [https://portal.azure.com/](https://portal.azure.com) and open **Microsoft Entra ID**.
2.  Click **Manage**, then **App registrations**. Open the **All applications** tab and click the app that you registered for Defguard. You can type its name in the search field to find it.

    <figure><img src="../../../.gitbook/assets/entra-cert-az-01-app-registrations.png" alt="App registrations list filtered to one app, with an arrow pointing at the app name"></figure>
3.  In the menu on the left, open **Manage** and click **Certificates & secrets**.

    <figure><img src="../../../.gitbook/assets/entra-cert-az-02-certificates-menu.png" alt="App overview with an arrow pointing at Certificates &#x26; secrets in the Manage menu"></figure>
4.  Click the **Certificates** tab.

    <figure><img src="../../../.gitbook/assets/entra-cert-az-03-certificates-tab.png" alt="Certificates &#x26; secrets page with an arrow pointing at the Certificates tab"></figure>
5.  Click **Upload certificate**.

    <figure><img src="../../../.gitbook/assets/entra-cert-az-04-upload.png" alt="Certificates tab with an arrow pointing at Upload certificate"></figure>
6.  Click the folder icon and select `defguard-entra.crt`. Enter a **Description**, for example `Defguard`, and click **Add**. Upload only the `.crt` file here, never the `.key` file.

    <figure><img src="../../../.gitbook/assets/entra-cert-az-05-select-and-add.png" alt="Upload certificate panel with arrows pointing at the folder icon and the Add button"></figure>
7.  The certificate appears in the list with its **Thumbprint** and **Expires** date. The expiry date matches the **Valid until** date that Defguard shows later.

    <figure><img src="../../../.gitbook/assets/entra-cert-az-06-added.png" alt="Certificates list with the uploaded certificate and an arrow pointing at its Expires date"></figure>

## Step 3: Configure Defguard

Follow the section that matches your setup:

* [Add a new Microsoft provider](certificate-authentication.md#add-a-new-microsoft-provider), if Microsoft is not connected to Defguard yet.
* [Switch an existing Microsoft provider to a certificate](certificate-authentication.md#switch-an-existing-microsoft-provider-to-a-certificate), if Microsoft already works with a client secret.

### Add a new Microsoft provider

1.  Go to **Settings** and open **External identity providers**. Click **Connect** in the **Microsoft** row.

    <figure><img src="../../../.gitbook/assets/entra-cert-01-providers.png" alt="External identity providers list with an arrow pointing at the Connect button in the Microsoft row"></figure>
2.  Fill in **Display name** and **Tenant ID**. Below **Tenant ID**, select **Upload certificate/private key**. Then fill in **Client ID**.

    <figure><img src="../../../.gitbook/assets/entra-cert-02-auth-method.png" alt="Client Settings step with an arrow pointing at the Upload certificate/private key option"></figure>
3.  Click **Upload certificate file** and select `defguard-entra.crt`. Then click **Upload key file** and select `defguard-entra.key`.

    <figure><img src="../../../.gitbook/assets/entra-cert-03-upload.png" alt="Client Settings step with arrows pointing at the Upload certificate file and Upload key file buttons"></figure>
4.  The buttons change to **Certificate** and **Private key** rows. This means that both files are loaded. Set the other options as described in the [Microsoft guide](./#configuring-microsoft-as-external-oidc-in-defguard) and click **Continue**.

    <figure><img src="../../../.gitbook/assets/entra-cert-04-continue.png" alt="Client Settings step with the Certificate and Private key rows filled and an arrow pointing at Continue"></figure>

    If a file is wrong, an error appears under it. See [Troubleshooting](certificate-authentication.md#troubleshooting).
5.  Optionally, turn on **Directory synchronization** and fill in its settings (see [Obtaining Directory Synchronization credentials](./#obtaining-directory-synchronization-credentials)). Directory synchronization uses the same certificate, so you do not need a client secret for it. Click **Continue**.

    <figure><img src="../../../.gitbook/assets/entra-cert-05-directory-sync.png" alt="Directory synchronization step with an arrow pointing at Continue"></figure>
6.  Defguard checks the certificate and the key and saves the provider. When you see **External ID provider successfully added**, click **Finish**.

    <figure><img src="../../../.gitbook/assets/entra-cert-06-finish.png" alt="Validation step showing External ID provider successfully added, with an arrow pointing at Finish"></figure>
7. Sign out and sign in with Microsoft to check that it works.

### Switch an existing Microsoft provider to a certificate

1.  Go to **Settings** and open **External identity providers**. Click **Edit** in the **Microsoft** row.

    <figure><img src="../../../.gitbook/assets/entra-cert-07-edit.png" alt="External identity providers list with an arrow pointing at the Edit button of the active Microsoft provider"></figure>
2. Select **Upload certificate/private key**.
3. Click **Upload certificate file** and select `defguard-entra.crt`. Then click **Upload key file** and select `defguard-entra.key`.
4. Scroll down and click **Save changes**.
5. Sign out and sign in with Microsoft to check that it works.

When you save, Defguard deletes the client secret that it stored before. After you confirm that sign-in works, delete the old client secret in Microsoft Entra ID too (**Certificates & secrets**, **Client secrets** tab).

## Check when the certificate expires

Go to **Settings**, open **External identity providers**, and click **Edit** in the **Microsoft** row. The **Certificate** row shows **Valid until** with the expiry date.

<figure><img src="../../../.gitbook/assets/entra-cert-08-valid-until.png" alt="Edit external identity provider page with an arrow pointing at the Valid until date in the Certificate row"></figure>

When the certificate has expired, the row shows **Expired** in red instead.

<figure><img src="../../../.gitbook/assets/entra-cert-11-expired.png" alt="Certificate row showing Expired in red, with an arrow pointing at it"></figure>

{% hint style="danger" %}
When the certificate expires, Microsoft rejects every request from Defguard. Users cannot sign in with Microsoft, and Directory synchronization stops. Replace the certificate before the expiry date.
{% endhint %}

### Email reminders

Defguard checks the certificate once a day. It emails all Defguard admins two weeks, one week, three days and one day before the certificate expires, and again when it expires. The emails are sent only when [email notifications (SMTP)](../../notifications/setting-up-smtp-for-email-notifications.md) are set up.

## Replace the certificate before it expires

Microsoft Entra ID accepts more than one certificate for the same app. Add the new certificate in Microsoft before you change Defguard, and users can keep signing in during the change.

1. Create a new certificate and private key, as in [Step 1](certificate-authentication.md#step-1-create-a-certificate-and-a-private-key). Use different file names, for example `defguard-entra-2026.crt` and `defguard-entra-2026.key`.
2. Upload the new certificate to Microsoft Entra ID, as in [Step 2](certificate-authentication.md#step-2-upload-the-certificate-to-microsoft-entra-id). Do not delete the old certificate yet.
3.  In Defguard, go to **Settings**, open **External identity providers**, and click **Edit** in the **Microsoft** row. Click the trash icon in the **Certificate** row and in the **Private key** row.

    <figure><img src="../../../.gitbook/assets/entra-cert-09-delete.png" alt="Edit page with arrows pointing at the trash icons in the Certificate and Private key rows"></figure>
4. Click **Upload certificate file** and select the new certificate. Click **Upload key file** and select the new private key.
5.  Click **Save changes**.

    <figure><img src="../../../.gitbook/assets/entra-cert-10-save.png" alt="Edit page with the new Certificate and Private key rows and an arrow pointing at Save changes"></figure>
6. Click **Edit** in the **Microsoft** row again. Check that **Valid until** shows the date of the new certificate.
7. Sign in with Microsoft to check that it works. Then delete the old certificate in Microsoft Entra ID.

{% hint style="info" %}
If your certificate authority renewed the certificate for the same private key, replace only the certificate. Defguard keeps the stored private key when you upload only a new certificate.
{% endhint %}

## Switch back to a client secret

1. Create a client secret in Microsoft Entra ID (**Certificates & secrets**, **Client secrets** tab, **New client secret**). Copy its **Value**.
2. In Defguard, go to **Settings**, open **External identity providers**, and click **Edit** in the **Microsoft** row.
3. Select **Use client secret**, paste the value into **Client secret**, and click **Save changes**.

Defguard deletes the stored certificate and private key when you save.

## Troubleshooting

### An error appears under the certificate or the private key

| Message in Defguard                                                         | Cause                                                                                             | What to do                                                                                                   |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Invalid file format                                                         | The file is not a PEM certificate or PEM private key, or you selected the files in the wrong order. | Upload the `.crt` file as the certificate and the `.key` file as the key. Convert `.pfx` files as shown in [Step 1](certificate-authentication.md#if-you-have-a-pfx-or-p12-file). |
| The certificate or private key is invalid, or they don't match              | The key does not belong to the certificate, the key is not RSA, or the key is password-protected. | Upload the key that was created together with the certificate. Create a new RSA key pair if necessary.       |
| The uploaded certificate has expired. Please upload a valid certificate.    | The expiry date of the certificate is in the past.                                                | Create a new certificate.                                                                                    |
| The uploaded certificate is not valid yet                                   | The start date of the certificate is in the future, or the clock of the Defguard server is wrong. | Wait until the start date, or correct the server clock.                                                     |
| The certificate could not be parsed                                         | The certificate file is damaged or incomplete.                                                    | Export or create the certificate again.                                                                      |
| Please upload the certificate and private key, or choose another option.    | One of the two files is missing.                                                                  | Upload both files.                                                                                           |

### Sign-in with Microsoft fails after you set up the certificate

If Defguard saved the provider but sign-in or Directory synchronization fails, Microsoft does not accept the certificate. The Defguard Core logs usually show a Microsoft error code, for example `AADSTS700027`. Check that:

* The certificate in Defguard is also uploaded to Microsoft Entra ID, in the same app registration whose **Client ID** you entered in Defguard.
* The certificate is not expired in Microsoft Entra ID.
* The **Tenant ID** and **Client ID** in Defguard match the app registration.
