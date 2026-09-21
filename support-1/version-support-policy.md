---
description: >-
  This page explains how long each Defguard version receives updates and which
  versions are currently supported.
---

# Version support policy

### Support policy

A Defguard version is supported for **3 months after the next version is published**. Publishing a new version therefore starts a 3-month overlap period, during which the previous version still receives updates and you can plan your upgrade.

Version **1.6 is an exception**: it was released as a Long Term Support (LTS) version and is supported for **6 months** after the next version was published, instead of 3.

Support always runs from the publication date of the _following_ version, not from the version's own release date. For example, 1.5 was published on 2025-09-15, and 1.6 was published on 2025-12-19, so 1.5 was supported until 2026-03-19.

### What support includes

While a version is supported, it receives:

* **Security fixes** for vulnerabilities found in that version, including issues reported by users and findings from our regular penetration tests.
* **Bug fixes** for defects affecting that version.

Fixes are delivered as patch releases (for example 2.1.1, 2.1.2, …) on that version's release line. New features are not backported to supported versions, they are only available in the latest version.

Once a version reaches its end of support date, it no longer receives any updates, including security fixes. Upgrade to a supported version before that date.

### Version table

| Version   | Published  | End of support |
| --------- | ---------- | -------------- |
| 1.3       | 2025-05-13 | 2025-09-30     |
| 1.4       | 2025-06-30 | 2025-12-15     |
| 1.5       | 2025-09-15 | 2026-03-19     |
| 1.6 (LTS) | 2025-12-19 | 2026-10-30     |
| 2.0       | 2026-04-30 | 2026-12-04     |
| 2.1       | 2026-09-04 | -              |

The table covers latest major versions. Patch releases (such as 1.6.7 or 2.0.3) do not extend or change the end of support date of their version.

### Staying up to date

* Always run the latest patch release of the version you are on; it contains all security and bug fixes published for that version so far.
* Plan your upgrade during the overlap period, before the version you use reaches its end of support date.
* New releases are announced on our [GitHub releases page](https://github.com/DefGuard/defguard/releases) and on our [blog](https://defguard.net/blog/).
