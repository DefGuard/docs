# Enrollment URL Changed

If the enrollment URL changes — for example, from `enroll.company.com` to `setup.company.com` — all existing desktop and mobile client installations must be updated manually with the new URL.

This is intentional. Neither the user nor a potential attacker can silently update the enrollment endpoint. The update requires a token issued by an administrator and an explicit action by the user.

### Process

**Administrator steps:**

Send an update token to each affected user. See the [#restarting-enrollment-manually](../../../features/remote-user-enrollment/#restarting-enrollment-manually "mention") for how to generate and send these tokens.

**User steps:**

Update the enrollment URL in the desktop or mobile client, providing the new URL and the token received from the administrator. See the [desktop-client](../../../using-defguard-for-end-users/desktop-client/ "mention") for the exact steps.
