# Device posture verification

{% hint style="warning" %}
**Availability**

This feature requires an Enterprise plan. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

{% hint style="warning" %}
**Posture verification feature is available starting with Defguard Core v2.1 and Defguard Client v2.1.**

Once posture checks are assigned to a location, that location does not appear in Defguard clients older than 2.1.

Older clients do not support posture verification and cannot access posture-protected locations.
{% endhint %}

Posture checks let you verify the security state of a user’s device before allowing it to connect to a location. Instead of trusting only the user’s identity, Defguard can also evaluate device requirements such as:

* Defguard client version
* Windows version
* Windows security update age
* Windows Active Directory membership
* Windows antivirus status
* Windows disk encryption status
* macOS version
* macOS disk encryption status (stand-alone package only)
* macOS device integrity status
* Linux kernel version
* Linux disk encryption status
* iOS version
* Android version
* Android device integrity status

If a posture check fails, the user is shown a list of the unmet requirements:

<figure><img src="../.gitbook/assets/image (349).png" width="375"><figcaption></figcaption></figure>

## Configuring posture checks

You can view, edit, create, and assign posture checks to locations in the "Posture Checks" section:

<figure><img src="../.gitbook/assets/image (350).png"><figcaption></figcaption></figure>

The section displays a table of all posture checks defined in the system. Clicking a posture name displays a handy drawer with posture details:

<figure><img src="../.gitbook/assets/image (351).png"><figcaption></figcaption></figure>

### Creating a new posture check

To create a new posture check, click the “Add new posture check” button above the posture checks table. This launches a wizard that guides you through the configuration step by step.

In the wizard, you can choose which operating systems the posture check applies to and define the requirements each device must meet. Depending on the selected platform, this can include minimum OS version, security update age, disk encryption, antivirus status, Active Directory membership, or device integrity. You can also set the minimum required Defguard client version and decide whether pre-release client versions are allowed.

{% hint style="warning" %}
If you do not configure requirements for a specific operating system, devices running that operating system will not be allowed to connect to locations protected by this posture check.

For example, if a posture check only defines Windows and macOS requirements, Linux, iOS, and Android devices will fail the check by default. This ensures that access is granted only to platforms that were explicitly included in the posture policy.
{% endhint %}

<figure><img src="../.gitbook/assets/image (352).png"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (353).png"><figcaption></figcaption></figure>

Once the rules are configured, the posture check can be saved and [assigned to one or more locations](device-posture-verification.md#assigning-posture-check-to-locations).

### Editing an existing posture check

To edit one of the existing posture checks, select the "Edit" menu item from the action menu in the posture checks table:

<figure><img src="../.gitbook/assets/image (354).png"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (355).png" width="563"><figcaption></figcaption></figure>

### Assigning posture check to locations

Posture checks can be assigned to locations in 4 ways:

* "Assign to locations" action from the actions menu in the postures table
* "Assign to locations" action from the "Actions" menu in the posture details drawer
* [Edit posture form](device-posture-verification.md#editing-an-existing-posture-check)
* "Posture Checks" section in the Location edit form

<figure><img src="../.gitbook/assets/image (356).png"><figcaption></figcaption></figure>

Once a posture check is assigned to a location, every client attempting to connect to that location is verified before access is granted. If multiple posture checks are assigned, the client must satisfy all requirements from all assigned posture checks. The connection is allowed only when every required check passes; if any requirement fails, the client is denied access.

### Duplicating a posture check

If you want to create a posture check that is similar to an existing one, you can duplicate the existing posture check instead of starting from scratch. This copies its configuration and lets you adjust only the parts that need to be different, saving time and reducing the risk of mistakes.

To duplicate a posture check, use the “Duplicate” action from the posture checks table or from the action menu in the posture check details drawer.

<figure><img src="../.gitbook/assets/image (357).png"><figcaption></figcaption></figure>

The posture check is duplicated and saved immediately, then opened in the edit form. You can modify any settings that should differ from the original and assign the duplicated posture check to the appropriate locations. Duplicated posture checks are not assigned to any locations by default.
