# License limits

## How to check license limits and current usage?

### General information

1.  Click on limits in top right corner<br>

    <figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 12.26.59.png" alt=""><figcaption></figcaption></figure>
2.  In this view, you can check how much of your limit is left.

    <figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 12.33.17.png" alt=""><figcaption></figcaption></figure>

### Detailed information

1.  Go to "Settings"<br>

    <figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 12.35.51.png" alt=""><figcaption></figcaption></figure>
2.  Go to "License" tab<br>

    <figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 12.38.02.png" alt=""><figcaption></figcaption></figure>
3.  Check "License management" section<br>

    <figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 12.39.31.png" alt=""><figcaption></figcaption></figure>

Here you can check:

* Current plan
* License type
* Support type
* Licence expiry date
* Current usage
* Available limits
* Enterprise features (Feature flags)

## Trying to exceed license limits

When trying to perform action that would exceed your current limits (for example, creating a user). Your action will be blocked and modal will be shown.

<figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 14.57.52.png" alt=""><figcaption></figcaption></figure>

## What will happen after reaching license limits?

After reaching license limits you will still be able to use Business/Enterprise features.&#x20;

However, creating new locations/users will be blocked (depending on which limits you have exceeded).

<figure><img src="../.gitbook/assets/Screenshot 2026-09-24 at 15.09.59.png" alt=""><figcaption></figcaption></figure>

## What to do after reaching license limits?

Defguard doesn't let you go over the user limit of your license. Once the limit is reached, you can't add new users, and no new accounts are created through LDAP or OpenID directory synchronization.

In rare edge cases the limit can still be exceeded. This shouldn't happen during normal use, but if it does, nothing is deleted or disabled and VPN connections keep working. However, Defguard stops treating the license as valid. All Business and Enterprise features are turned off, logging in through OpenID is not possible, LDAP synchronization is switched off in the settings, and Directory Sync stops running.
