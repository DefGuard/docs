---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/using-defguard-for-end-users/desktop-client/mtu-setting
---

# MTU Setting

Defguard client supports manual MTU configuration to ensure stable VPN connections on mobile and carrier-grade networks. These networks often enforce lower MTU limits, which can cause VPN traffic to be dropped even though the tunnel appears connected.

Adjusting the MTU reduces packet size so traffic fits within network constraints, improving reliability without server-side changes. Typical working values are in the **1300-1500** range.

<figure><img src="../../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

***

