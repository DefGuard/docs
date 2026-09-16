# Disconnected after MFA timeout

When Multi-Factor Authentication is enabled for a VPN location, the gateway operates differently from a non-MFA location. Without MFA, peers (devices) are persistent — they remain in the WireGuard kernel configuration at all times. With MFA enabled, a peer is only added to the gateway after the user completes a successful MFA challenge and a per-session pre-shared key is negotiated between the client and gateway.

As a result, there is a session lifetime tied to the MFA authentication.

### Client disconnect threshold

Each location has a **Client disconnect threshold (seconds)** setting. This value controls how long the gateway waits before removing an inactive peer from its configuration.

To find this setting: open the location in the Defguard web UI, go to the location's settings, and look for **Client disconnect threshold (seconds)**.

If you are being disconnected sooner than expected:

1. Check the current threshold value for the location.
2. Increase it if the disconnects are happening due to brief periods of inactivity rather than a genuine session expiry.

### Reconnecting after disconnect

After the gateway removes the peer, the client may not detect the disconnection immediately and can show the connection as still active while traffic is actually not flowing. If you suspect this has happened, disconnect manually and reconnect. This forces a new MFA challenge and re-establishes the session.
