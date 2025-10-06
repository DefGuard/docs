# Remote user enrollment

By design **Defguard core** is meant to be deployed **securely** within your infrastructure and only accessible from within the internal network or by VPN.

This introduces an issue with onboarding **new users** and forces the admin to choose an initial password, setup a VPN device for them, and pass on those details to the end user using possibly **insecure** channels.

To avoid this issue you can deploy a **public** [Defguard proxy](https://github.com/DefGuard/proxy) which enables a **secure enrollment process.**

**Here is a video showcasing:**

* how admin adds a user with secure remote enrollment
* then how the enrollment process looks like for the user

{% embed url="https://www.youtube.com/watch?v=KdyZoAlBb9k" %}

{% hint style="info" %}
The proxy is included when using the default [deployment instructions](../../deployment-strategies/setting-up-your-instance.md).

Please also see the relevant configuration options for [core](../../deployment-strategies/configuration.md#enrollment-configuration) and the [proxy itself](../../deployment-strategies/configuration.md#enrollment-service).
{% endhint %}

### How to initiate user secure enrollment

When adding a new user please select the option: **Use user self-enrollment process:**

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 22.09.36.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**By enabling this option, the admin will only provide the user data and will not be able to set the user’s password, the user will create their own password during the enrollment process in the desktop client.**
{% endhint %}

After filling out the user data, there are two options to start the process for the user:

<figure><img src="../../.gitbook/assets/Screenshot 2025-09-23 at 22.12.55.png" alt="" width="563"><figcaption></figcaption></figure>

1. Automatic: _**Sending token by email** (_&#x74;his requires for SMTP to be configured) - the user will receive an email will all the instructions how to initiate the Enrollment process
2. Manual: _**Deliver the token yourself**_ — this will only display the URL and token that must be handed over to the user personally.

{% hint style="info" %}
The email address you specify for delivering the enrollment token can be any email available to the user. It **does not** have to be the same one used when creating an account as we assume that a new user does not yet have access to their official company email account.
{% endhint %}

When the user adds a Defguard instance in the Desktop client using the received token, not only is the VPN client configured, but the user can also:

* set up their password
* configure MFA, which is required to connect to MFA-protected locations

This means the user may not even have access to Defguard itself, but can still configure both VPN and MFA!

{% hint style="danger" %}
**For MFA configuration to be mandatory during the enrollment process, there must be at least one VPN location with MFA enabled. Otherwise, MFA setup will remain optional.**
{% endhint %}

### Restarting enrollment manually

If there are any issues with the enrollment process (failed notification delivery, a lost token etc) you can restart it:

* Go to **Users** page
* Find the relevant user and click on the **Action** button on the right
* A **Configure Desktop Client** option should be available in the pop-over menu

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-01 at 5.43.32 PM.png" alt="" width="563"><figcaption></figcaption></figure>

* Clicking it will open the same enrollment modal where you can choose how to deliver the enrollment token (by email if SMTP is configured or manually):

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-01 at 5.45.59 PM.png" alt="" width="563"><figcaption></figcaption></figure>

### Performing remote enrollment (as a user)

As a new user, after an admin starts the enrollment process, you will receive your enrollment token.

If you receive an **email notification**, just click the link, and you'll be redirected to the enrollment wizard.

If the admin decides to deliver your token through some other secure means, you'll have to go the specified enrollment page and enter the token **manually**.

By following the **enrollment wizard,** you'll be able to do the following:

* verify that your data is correct
* activate your user account
* choose your password
* add an initial device for VPN access

After completing the wizard, you should be able to connect to the VPN and access the main Defguard web UI.

## Enrollment settings

{% hint style="warning" %}
In order for the enrollment process to function correctly you must also [set up an SMTP server](../notifications/setting-up-smtp-for-email-notifications.md) for delivering email notifications.
{% endhint %}

As an admin, you can configure enrollment-related settings on the **Enrollment** page. This includes:

* Making the VPN device step optional or mandatory in the enrollment wizard
* Customizing the user [onboarding messages](user-onboarding-after-enrollment.md).

#### Message template tags

There are several **template tags** (similar to [Jinja2](https://jinja.palletsprojects.com/en/3.1.x/) tags) that you can use in the onboarding messages to insert some dynamic content:

* `{{ first_name }}` - newly created user first name
* `{{ last_name }}` - newly created user last name
* `{{ username }}` - newly created user username/login
* `{{ admin_first_name }}` - first name of the administrator who initiated the enrollment process
* `{{ admin_last_name }}` - last name of the administrator who initiated the enrollment process
* `{{ admin_phone }}`- phone number of the administrator who initiated the enrollment process
* `{{ admin_email }}`- email of the administrator who initiated the enrollment process
* `{{ defguard_url }}`- internal Defguard URL (your Defguard instance address)
* `{{ defguard_version }}`
