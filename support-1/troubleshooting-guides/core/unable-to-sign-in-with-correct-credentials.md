# Unable to sign in with correct credentials

This error appears when the browser's session cookie cannot be set. The most common cause is a misconfigured `DEFGUARD_URL`.

### In version 2.0

`DEFGUARD_URL` is deprecated. The instance URL is now configured through the web UI. Go to **Settings** in the Defguard interface and verify the **Instance URL** field. It must match the URL you are accessing Defguard from, including the correct scheme (`https://` vs `http://`).

If the value is wrong, the server will set the session cookie with an incorrect domain or path, causing the browser to reject or discard it.

### HTTP (non-TLS) deployments

If you are accessing Defguard over plain `http://` (for example, in a local or development environment), you need to configure auth cookie settings to allow non-secure cookies. See the [Core deployment parameters](../../../deployment-strategies/configuration.md#core-deployment-parameters) documentation for the required settings.
