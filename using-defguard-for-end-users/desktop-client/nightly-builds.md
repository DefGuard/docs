# Nightly builds

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
