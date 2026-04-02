---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/updating-and-version-compatibility
---

# Updating and version compatibility

Each service in the Defguard stack can be updated independently, provided the components remain compatible. When components connect, Defguard automatically performs a version check. If an incompatibility is detected, the connection is refused and it will be clearly reported both in the log files and through a dialog in the core UI:

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
**Note on Multiple Gateways per Location**

If you configure more than one gateway for the same location, they will overwrite each other’s incompatibility data. This happens because location is currently used as the identifier for gateways.

This limitation will be resolved once full high-availability (HA) support is implemented.
{% endhint %}

It's recommended to always use newest version of services and update them all together to avoid incompatibility.\
Check the GitHub repositories for each service to find their newest releases and release notes.

* Docker - For Docker and Kubernetes based setup just change docker image version for service you want to update.
* Packages(DEB, RPM, etc.) - Currently we don't have any package repository so if you want to update your service installed as package you have to download new version from service repository.

**GitHub Repositories:**

* [Defguard Core](https://github.com/DefGuard/defguard/releases)
* [Defguard Proxy](https://github.com/DefGuard/proxy/releases)
* [Defguard Gateway](https://github.com/DefGuard/gateway/releases)
* [Defguard YubiBridge](https://github.com/DefGuard/YubiKey-Provision/releases)
