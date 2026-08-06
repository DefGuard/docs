# Pre-production and development releases

To test any pre-production or development release:

### One-line install

The simplest way to test the latest development or pre-release version is to use one line installation method with the appropriate argument. More on that in [the one-line install documentation](../getting-started/one-line-install.md).

### Nightly builds

Nightly builds of Desktop Client are published at [https://nightly.defguard.net](https://nightly.defguard.net/). They are built automatically every night from the latest commit on each active branch.

Unlike pre-releases, nightly builds are snapshots of work in progress. They have no release notes, no version number of their own, and no guarantee of working at all. Use them to verify a fix or try a feature before it is released.

{% hint style="danger" %}
Nightly builds are not release candidates and must not be used in production.
{% endhint %}

| Platform        | File                                     |
| --------------- | ---------------------------------------- |
| Debian / Ubuntu | `defguard-client-<arch>-<commit>.deb`    |
| Fedora / RHEL   | `defguard-client-<arch>-<commit>.rpm`    |
| macOS           | `defguard-client-universal-<commit>.pkg` |

`<arch>` is either `x86_64` or `aarch64`. `<commit>` is the short Git commit the package was built from. Always include it when reporting an issue with a nightly build, as there is no version number to identify the build by.

### Binaries and packages

Each GitHub repository ([core](https://github.com/DefGuard/defguard/releases), [gateway](https://github.com/DefGuard/gateway/releases), [proxy](https://github.com/DefGuard/proxy/releases), and [client](https://github.com/DefGuard/client/releases)) has its **pre-release versions** available on the GitHub release page. This is where you can download binaries or packages with the pre-release, e.g.:

<figure><img src="../.gitbook/assets/Screenshot 2025-08-01 at 13.38.44.png" alt=""><figcaption></figcaption></figure>

### Docker images

Each Docker image for [core](https://github.com/DefGuard/defguard/pkgs/container/defguard), [gateway](https://github.com/DefGuard/gateway/pkgs/container/gateway) and [proxy](https://github.com/DefGuard/proxy/pkgs/container/defguard-proxy) has the following tags:

* `pre-release` – this tag is for the **latest pre-production release** - which also contains a version in form of `vX.Y.Z-alpha/beta/rcX` from the `main` branch
* `dev` – this tag is for the latest development release from the `dev` branch.

#### Docker compose

Please change the Docker compose file to match the version or tags as stated above.
