# Unable to sign in to your defguard instance with correct credentials

The user tries to sign in to a defguard instance but gets a 401 response with message "Session is required".

This issue is most likely caused by a misconfigured `DEFGUARD_URL` . Please take a look at the configuration options described in [General configuration](../../configuration.md#general-configuration) documentation.

If you want to access your defguard instance without TLS (using an `http://` URL), please also make sure you have everything configured according to [Auth cookies configuration](../../configuration.md#auth-cookies-configuration) documentation.
