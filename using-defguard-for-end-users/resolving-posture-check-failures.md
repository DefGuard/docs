# Resolving Posture Check Failures

Posture checks help ensure that your device meets your organization’s security requirements before it can connect to a protected location. In addition to verifying your user account, Defguard may check device properties such as the operating system version, disk encryption status, antivirus status, system integrity, security update age, or Defguard client version. If your device does not meet the required posture policy, the connection will be blocked and Defguard will show which checks failed so you can resolve them or contact your administrator for help.

<figure><img src="../.gitbook/assets/image (367).png" alt="" width="375"><figcaption></figcaption></figure>

When a posture check fails, the device does not meet one or more requirements defined for the location it is trying to access. This section explains how to identify the failing checks, understand what they mean, and resolve the most common issues so the device can connect successfully.

### **operating system is not allowed**

The posture check has no rule for the device’s operating system. For example, the policy defines Windows and macOS only, but the device is Linux.

**Fix**: Contact your administrator.

### `client version is too old`

The Defguard client version is below the minimum version required by the posture check.

**Fix:** Update your Defguard client to the latest version.

### `pre-release client versions are not allowed`

The device is using a pre-release Defguard client version, but the posture check does not allow pre-release clients.

**Fix:** Update your Defguard client to the latest full version.

### `OS version is too old`

The device OS version is below the minimum configured for that operating system. Applies to Windows, macOS, iOS, and Android OS version rules.

**Fix:** Upgrade your operating system / contact your administrator.

### `kernel version is too old`

The Linux kernel version is below the minimum configured kernel version.

**Fix:** Upgrade your system/kernel to the latest stable version.

### `disk encryption is required`

Disk encryption is required by the posture check, but the device reported that disk encryption is not enabled.

#### Windows

On windows disk encryption can be turned on by enabling [BitLocker](https://support.microsoft.com/en-us/windows/bitlocker-overview-44c0c61c-989d-4a69-8822-b95cd49b1bbf). Find the "Manage BitLocker" item in menu start:

<figure><img src="../.gitbook/assets/image (358).png" alt="" width="563"><figcaption></figcaption></figure>

Then click the "Turn on BitLocker" option:

<figure><img src="../.gitbook/assets/image (359).png" alt="" width="563"><figcaption></figcaption></figure>

Once you complete the BitLocker wizard, your device should be ready to connect to Defguard.

#### Linux

On Linux, disk encryption is most commonly implemented using LUKS/dm-crypt for full-disk or volume encryption, often together with LVM for managing encrypted volumes. Setting up encrypted Linux storage is outside the scope of this documentation, so refer to the official documentation for your distribution:

* [Ubuntu](https://documentation.ubuntu.com/security/security-features/storage/encryption-full-disk/)
* [Red Hat](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/security_hardening/encrypting-block-devices-using-luks_security-hardening)
* [Arch](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system)
* [NixOS](https://wiki.nixos.org/wiki/Full_Disk_Encryption)

### `antivirus is required`

Antivirus is required by the posture check, but the device reported that antivirus is not present or active.

**Fix:** Enable windows real-time protection or install a 3rd party antivirus software.

Find the "Virus & threat protection" item in Menu Start:

<figure><img src="../.gitbook/assets/image (360).png" alt="" width="563"><figcaption></figcaption></figure>

Click "Manage settings" below "Virus & threat protection settings" section:

<figure><img src="../.gitbook/assets/image (361).png" alt="" width="563"><figcaption></figcaption></figure>

Turn on the "Real-time protection":

<figure><img src="../.gitbook/assets/image (362).png" alt="" width="563"><figcaption></figcaption></figure>

### `Active Directory domain join is required`

The posture check requires Windows Active Directory domain membership, but the device is not joined to an Active Directory domain.

**Fix:** Join active directory.

{% hint style="warning" %}
To join a Windows device to Active Directory you'll need Windows Pro,

Enterprise or Education. Windows Home cannot join an AD domain.
{% endhint %}

**First set the DNS to your domain controller or AD DNS server.**

* Go to Settings -> Network & internet
* Click "Edit" next to "DNS server assignment".

<figure><img src="../.gitbook/assets/image (363).png" alt="" width="563"><figcaption></figcaption></figure>

* Select "Manual" DNS setting
* Enable "IPv4" (IPv6 if you want to configure IPv6)
* Type in the IP address of your AD DNS server into "Preferred DNS" input.
* Save

<figure><img src="../.gitbook/assets/image (364).png" alt="" width="375"><figcaption></figcaption></figure>

**Configure the domain**

* Settings -> System -> About -> Domain or workgroup
* Click "Change" next to "Rename this computer or change its domain or workgroup"

<figure><img src="../.gitbook/assets/image (365).png" alt="" width="352"><figcaption></figcaption></figure>

Enter the domain and configure credentials:

<figure><img src="../.gitbook/assets/image (366).png" alt="" width="413"><figcaption></figcaption></figure>

Once the domain is configured, you should be able to connect to Defguard.

### `last Windows security update is X days old`

The Windows device has not received a security update within the maximum allowed age configured in the posture check.

**Fix:** Update your system.

### `device integrity check failed`

Device integrity is required, but the device did not pass the integrity check. On macOS this means System Integrity Protection is disabled.

{% embed url="https://developer.apple.com/documentation/security/disabling-and-enabling-system-integrity-protection#Enable-System-Integrity-Protection" %}

1. Restart your computer in [Recovery mode](https://support.apple.com/en-us/HT201314).
2. Launch Terminal from the Utilities menu.
3. Run the command `csrutil enable`.
4. Restart your computer.

### `posture data is missing`

The client did not send any posture data. This usually means the client does not support posture checks or failed to collect/send the posture payload.

**Fix:** Update your Defguard client to the latest version.

