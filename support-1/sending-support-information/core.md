# Core

Defguard Core has a dedicated **Support** page that gathers everything you need when reporting a problem or contacting our team. It is available to administrators in the main menu, under the **Admin** group.

The page is divided into sections:

### Documentation

Before submitting an issue, check the documentation - most configuration problems are covered there. The **Go to documentation** button opens it in a new tab.

### Report a bug

The **Report on Github** button opens a new issue in our GitHub repository. To give us more context, use the **Download** button next to it and pick **Download support data**. This saves a JSON file named `defguard-support-data-<timestamp>.json`, which you can attach to the issue.

The support data contains your instance settings, VPN locations, and device assignments, so that we can reproduce your configuration. Secrets are removed before the file is generated - the SMTP password and the LDAP bind password are stripped - and user records are reduced to anonymous diagnostic fields (identifier, MFA method, whether MFA is enabled, whether the account is active and enrolled). No usernames, e-mail addresses, or password hashes are included.

{% hint style="info" %}
Core logs are **not** part of the support data file. If we ask you for logs, collect them from the machine or container running Core. If Core was started with `--log-file` / `DEFGUARD_LOG_FILE` (see [Core deployment parameters](../../deployment-strategies/configuration.md#core-deployment-parameters)), an administrator can also fetch that file from the API endpoint `GET /api/v1/support/logs`.
{% endhint %}

### Request feature

The **Submit on Github** button opens a feature request in our GitHub repository.

### Contact us by email

Shown for instances with a basic or direct support plan. It points you at `support@defguard.net`.

### Need direct support?

Shown only for instances with a direct support plan. **Open a support ticket** opens a ticket pre-filled with your customer identifier, and **Schedule a call with our support team** opens our booking page.
