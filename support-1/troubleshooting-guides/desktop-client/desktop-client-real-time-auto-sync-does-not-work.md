# Desktop client real-time / auto sync does not work

The desktop client communicates with core through the Edge component (the enrollment and proxy service). Real-time sync, automatic configuration updates, and MFA handshakes all depend on this connection. If Edge is unreachable or misconfigured, these features stop working silently.

### Common causes

**Edge is not running or not reachable**

Verify the Edge service is up and the client machine can reach it over HTTP/HTTPS on the configured port (default: `8080`). Try opening the Edge enrollment URL from the same machine running the client.

**Edge public URL is set incorrectly in core**

In version 2.0 the enrollment URL is configured through the web UI, not via an environment variable. Go to **Settings** in the Defguard web interface and check the **Edge public URL** field. If this value is wrong or left as the default `http://localhost:8080`, the client will attempt to connect to localhost on the server rather than the correct public address.

Set it to the actual public URL your Edge component is accessible at, for example `https://enroll.company.com`.

**TLS mismatch or certificate error**

If Edge is behind a reverse proxy with TLS, confirm the certificate is valid and that the client trusts it. **Self-signed** certificates **require** manual trust configuration.

### Verification steps

1. Open the Defguard web UI and navigate to **Settings**.
2. Confirm the Edge public URL is correct and reachable from client machines.
3. From the client machine, perform a GET request to the Edge URL to confirm it responds.
4. Check Edge logs for connection errors: `journalctl -u defguard-proxy -n 200`
5. Check core logs for enrollment or proxy-related errors: `journalctl -u defguard -n 200`

See also: [architecture](../../../in-depth/architecture/ "mention")
