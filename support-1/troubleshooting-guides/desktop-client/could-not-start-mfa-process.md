# Could not start MFA process

When setting up MFA in Defguard, you may see the following error in the desktop client after entering the code: **"Could not start MFA process"**.

The client debug logs may also show:

```
Failed to deserialize the JSON body into the target type: code: invalid type: string "005325", expected u32 at line 1 column 268.
```

This error means one or more components are running an outdated version that is incompatible with the current API contract. Check and update the following:

* Defguard core
* Defguard Edge Component
* Desktop client application

All three must be on compatible versions. Update them to the latest stable releases and retry.

Download links: [https://defguard.net/download/](https://defguard.net/download/)
