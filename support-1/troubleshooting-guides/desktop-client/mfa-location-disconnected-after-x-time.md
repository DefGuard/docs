# MFA location disconnected after X-time

location, After enabling Multi-Factor Authentication for a location, the configuration of the gateway changes. Without MFA, peers (devices) are persistent (always in the Kernel memory) and gateway only changed the gateway configuration if a peer is added/removed/changed.

But when MFA is enabled, peers **are only added to the gateway** after successful MFA on the client (and pre-shared key exchange with client and gateway to establish a dedicated key for the session).

Also, there is a setting in the location named _Client disconnect threshold (seconds):_

<figure><img src="../../../.gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

This setting specifies that if the **peer is inactive for (defined seconds)**\_, the gateway **should remove it from the configuration** (as it should not be persistent since MFA is required).

So if you are disconnected from the location:

1. Check what is your setting for the client disconnect threshold.
2. Make sure you are on the latest version of the client.
