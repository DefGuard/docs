# Device posture verification

{% hint style="warning" %}
**Availability**

This feature requires an Enterprise plan. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

{% hint style="warning" %}
**Posture verification feature is available starting with Defguard Core v2.1 and Defguard Client v2.1.**

Once posture checks are assigned to a location, devices running a Defguard client older than 2.1 will not be able to access it.
{% endhint %}

Posture checks let you verify the security state of a user’s device before allowing it to connect to a location. Instead of trusting only the user’s identity, Defguard can also evaluate device requirements such as:

* Defguard client version
* Windows version
* Windows security update age
* Windows Active Directory membership
* Windows antivirus status
* Windows disk encryption status
* macOS version
* macOS disk encryption status
* macOS device integrity status
* Linux kernel version
* Linux disk encryption status
* iOS version
* Android version
* Android device integrity status

If a posture check fails, the user is shown a list of the unmet requirements:

<figure><img src="../.gitbook/assets/image (193).png" alt="" width="375"><figcaption></figcaption></figure>

## Configuring posture checks

You can view, edit, create, and assign posture checks to locations in the "Posture Checks" section:

<figure><img src="../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

The section displays a table of all posture checks defined in the system. Clicking posture name displays a handy drawer with posture details:

<figure><img src="../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

### Creating a new posture check

To create a new posture check, click the “Add new posture check” button above the posture checks table. This launches a wizard that guides you through the configuration step by step.

\
In the wizard, you can choose which operating systems the posture check applies to and define the requirements each device must meet. Depending on the selected platform, this can include minimum OS version, security update age, disk encryption, antivirus status, Active Directory membership, or device integrity. You can also set the minimum required Defguard client version and decide whether pre-release client versions are allowed.

{% hint style="warning" %}
If you do not configure requirements for a specific operating system, devices running that operating system will not be allowed to connect to locations protected by this posture check.

For example, if a posture check only defines Windows and macOS requirements, Linux, iOS, and Android devices will fail the check by default. This ensures that access is granted only to platforms that were explicitly included in the posture policy.
{% endhint %}

<figure><img src="../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

Once the rules are configured, the posture check can be saved and [assigned to one or more locations](device-posture-verification.md#assigning-posture-check-to-locations).

### Editing an existing posture check

To edit one of the existing posture checks, select the "Edit" menu item from the action menu in posture checks table:

<figure><img src="../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (345).png" alt="" width="563"><figcaption></figcaption></figure>

### Assigning posture check to locations

Posture checks can be assigned to locations in 4 ways:

* "Assign to locations" action from the actions menu in postures table
* "Assign to locations" action from the "Actions" menu in the posture details drawer
* [Edit posture form](device-posture-verification.md#editing-an-existing-posture-check)
* "Posture Checks" section in the Location edit form

<figure><img src="../.gitbook/assets/image (341).png" alt=""><figcaption></figcaption></figure>

Once a posture check is assigned to a location, every client attempting to connect to that location is verified before access is granted. If multiple posture checks are assigned, the client must satisfy all requirements from all assigned posture checks. The connection is allowed only when every required check passes; if any requirement fails, the client is denied access.

### Duplicating a posture check

If you want to create a posture check that is similar to an existing one, you can duplicate the existing posture check instead of starting from scratch. This copies its configuration and lets you adjust only the parts that need to be different, saving time and reducing the risk of mistakes.

\
To duplicate a posture check, use the “Duplicate” action from the posture checks table or from the action menu in the posture check details drawer.

<figure><img src="../.gitbook/assets/image (346).png" alt=""><figcaption></figcaption></figure>

The posture check is duplicated and saved immediately, then opened in the edit form. You can modify any settings that should differ from the original and assign the duplicated posture check to the appropriate locations.\
Duplicated posture checks are not assigned to any locations by default.

## Resolving Posture Check Failures

When a posture check fails, the device does not meet one or more requirements defined for the location it is trying to access. This section explains how to identify the failing checks, understand what they mean, and resolve the most common issues so the device can connect successfully.

### **operating system is not allowed**

The posture check has no rule for the device’s operating system. For example, the policy defines Windows and macOS only, but the device is Linux.

**Fix**: Contact your administrator.

### `client version {actual} is too old (required: {required})`

The Defguard client version is below the minimum version required by the posture check.

**Fix:** Update your Defguard client to the latest version.

### `pre-release client versions are not allowed`

The device is using a pre-release Defguard client version, but the posture check does not allow pre-release clients.

**Fix:** Update your Defguard client to the latest full version.

### `OS version {actual} is too old (required: {required})`

The device OS version is below the minimum configured for that operating system. Applies to Windows, macOS, iOS, and Android OS version rules.

**Fix:** Upgrade your operating system / contact your administrator.

### `kernel version {actual} is too old (required: {required})`

The Linux kernel version is below the minimum configured kernel version.

**Fix:** Upgrade your system/kernel to the latest stable version.

### `disk encryption is required`

Disk encryption is required by the posture check, but the device reported that disk encryption is not enabled.

#### Windows

On windows disk encryption can be turned on by enabling [BitLocker](https://support.microsoft.com/en-us/windows/bitlocker-overview-44c0c61c-989d-4a69-8822-b95cd49b1bbf). Find the "Manage BitLocker" item in menu start:

<figure><img src="../.gitbook/assets/image (347).png" alt="" width="563"><figcaption></figcaption></figure>

Then click the "Turn on BitLocker" option:

<figure><img src="../.gitbook/assets/image (348).png" alt="" width="563"><figcaption></figcaption></figure>

Once you complete the BitLocker wizard, your device should be ready to connect to Defguard.

#### Linux

On Linux, disk encryption is most commonly implemented using LUKS/dm-crypt for full-disk or volume encryption, often together with LVM for managing encrypted volumes. Setting up encrypted Linux storage is outside the scope of this documentation, so refer to the official documentation for your distribution:

* [Ubuntu](https://documentation.ubuntu.com/security/security-features/storage/encryption-full-disk/)
* [Red Hat](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/security_hardening/encrypting-block-devices-using-luks_security-hardening)
* [Arch](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system)
* [NixOS](https://wiki.nixos.org/wiki/Full_Disk_Encryption)

#### MacOS

TBD

### `antivirus is required`

Antivirus is required by the posture check, but the device reported that antivirus is not present or active.

**Fix:** Enable windows real-time protection or install a 3rd party antivirus software.

Find the "Virus & threat protection" item in Menu Start:

<figure><img src="../.gitbook/assets/image (349).png" alt="" width="563"><figcaption></figcaption></figure>

Click "Manage settings" below "Virus & threat protection settings" section:

<figure><img src="../.gitbook/assets/image (350).png" alt="" width="563"><figcaption></figcaption></figure>

Turn on the "Real-time protection":

<figure><img src="../.gitbook/assets/image (351).png" alt="" width="563"><figcaption></figcaption></figure>

### `Active Directory domain join is required`

The posture check requires Windows Active Directory domain membership, but the device is not joined to an Active Directory domain.

**Fix:** Join active directory.

{% hint style="warning" %}
To join a Windows device to Active Directory you'll need Windows Pro,&#x20;

Enterprise or Education. Windows Home cannot join an AD domain.
{% endhint %}

**First set the DNS to your domain controller or AD DNS server.**

* Go to Settings -> Network & internet
* Click "Edit" next to "DNS server assignment".

<figure><img src="../.gitbook/assets/image (353).png" alt="" width="563"><figcaption></figcaption></figure>

* Select "Manual" DNS setting
* Enable "IPv4" (IPv6 if you want to configure IPv6)
* Type in the IP address of your AD DNS server into "Preferred DNS" input.
* Save

<figure><img src="../.gitbook/assets/image (354).png" alt="" width="375"><figcaption></figcaption></figure>

**Configure the domain**

* Settings -> System -> About -> Domain or workgroup&#x20;
* Click "Change" next to "Rename this computer or change its domain or workgroup"

<figure><img src="../.gitbook/assets/image (355).png" alt="" width="352"><figcaption></figcaption></figure>

Enter the domain and configure credentials:

<figure><img src="../.gitbook/assets/image (356).png" alt="" width="413"><figcaption></figcaption></figure>

Once the domain is configured, you should be able to connect to Defguard.

### `last Windows security update is {actual_age_days} days old (max allowed: {required_max_age_days})`

The Windows device has not received a security update within the maximum allowed age configured in the posture check.

**Fix:** Update your system.

### `device integrity check failed`

Device integrity is required, but the device did not pass the integrity check. This applies to supported platforms such as macOS and Android.

**Fix:** TBD

### `posture data is missing`

The client did not send any posture data. This usually means the client does not support posture checks or failed to collect/send the posture payload.

**Fix:** Update your Defguard client to the latest version.
