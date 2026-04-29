---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/in-depth/client-application-feature-compatibility
---

# Client application feature compatibility

Defguard consists of multiple **server-side components** (Core, Gateway, Edge) and multiple **client applications** (Desktop, Mobile).

For the purpose of compatibility and feature availability, this documentation assumes that:

* all server-side components are deployed in the **same version**, and are collectively referred to as **Server**,
* the user-facing software (Desktop or Mobile) is referred to as the **Client**.

Use this page to quickly verify whether a given feature will work in your environment (Server and Client combinations).

### Feature compatibility matrix

<table><thead><tr><th width="187.515625">Feature</th><th width="140.56640625">Introduced in</th><th>Minimum Server version</th><th>Minimum Client version</th></tr></thead><tbody><tr><td><a href="../features/service-locations.md">Service Locations</a></td><td>1.6</td><td>1.6</td><td>1.6</td></tr><tr><td><a href="../using-defguard-for-end-users/desktop-client/mtu-setting.md">Setting MTU</a></td><td>1.6</td><td>Client-only feature</td><td>1.6</td></tr><tr><td><a href="../features/desktop-client-auto-provisioning/">Desktop Client Auto Provisioning</a></td><td>1.6</td><td>1.6</td><td>1.6</td></tr><tr><td><a href="../features/wireguard/behavior-customization.md#client-traffic-policy-selection">Client Traffic Policy Selection</a></td><td>1.6</td><td>1.6</td><td>1.6</td></tr></tbody></table>
