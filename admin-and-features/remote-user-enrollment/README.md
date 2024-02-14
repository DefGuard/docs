# Remote user enrollment

By design **defguard core** is meant to be deployed **securely** within your infrastructure and only accessible from within the internal network or by VPN.

This introduces an issue with onboarding **new users** and forces the admin to choose an initial password, setup a VPN device for them, and pass on those details to the end user using possibly **insecure** channels.

To avoid this issue you can deploy a **public** [defguard proxy](https://github.com/DefGuard/proxy) which enables a **secure enrollment process:**

<figure><img src="https://raw.githubusercontent.com/DefGuard/docs/docs/releases/0.7/enrollment.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
The proxy is included when using the default [deployment instructions](../setting-up-your-instance/).&#x20;

Please also see the relevant configuration options for [core](../setting-up-your-instance/configuration.md#enrollment-configuration) and the [proxy itself](../setting-up-your-instance/configuration.md#enrollment-service).&#x20;
{% endhint %}

## Enrollment settings

{% hint style="warning" %}
In order for the enrollment process to function correctly you must also [set up an SMTP server](../setting-up-smtp-for-email-notifications.md) for delivering email notifications.
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
* `{{ defguard_url }}`- internal defguard URL (your defguard instance address)
* `{{ defguard_version }}`

## Remote enrollment process

### Starting remote enrollment (as an admin)

* Go to **Users** page
* Click **Add new** user button
* Within the modal that appears fill in the new user's data as usual, but instead of entering a password check the **Use remote enrollment** checkbox
* Click the **Add user** button
* In the next modal choose whether you want to **Send token by email** or **Deliver token yourself**
* If you choose to deliver the enrollment token by email provide an email address to which a notification will be sent

{% hint style="info" %}
The email address you specify for delivering the enrollment token can be any email available to the user. It **does not** have to be the same one used when creating an account as we assume that a new user does not yet have access to their official company email account.
{% endhint %}

* Click **Start enrollment**
* If you choose to deliver the token yourself you'll be shown a URL and token that you can copy and pass to the user

### Restarting enrollment manually

If there are any issues with the enrollment process (failed notification delivery, a lost token etc) you can restart it:

* Go to **Users** page
* Find the relevant user and click on the **Action** button on the right
* A **Start enrollment** option should be available in the popover menu
* Clicking it will open the same **Start enrollment** modal where you can choose how to deliver the enrollment token

### Performing remote enrollment (as a user)

As a new user after an admin starts the enrollment process, you will receive your enrollment token.

If you receive an **email notification** just click the link and you'll be redirected the the enrollment wizard.

If the admin decides to deliver your token through some other secure means you'll have to go the the specified enrollment page and enter the token **manually**.

By following the **enrollment wizard** you'll be able to do the following:

* verify that your data is correct
* activate your user account
* choose your password
* add an initial device for VPN access

After completing the wizard you should be able to connect to the VPN and access the main defguard web UI.
