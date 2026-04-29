# Disconnecting shows "Connection failed" (Linux / NetworkManager)

When disconnecting from a location on a Linux system with `NetworkManager` installed, a **"Connection failed"** system notification appears. This is a visual bug only — the VPN connection itself is not affected.

The error occurs because `NetworkManager` attempts to manage the WireGuard network interfaces created by Defguard. When those interfaces are removed on disconnect, `NetworkManager` reports a failure.

### Fix

Tell `NetworkManager` to ignore all interfaces created by Defguard. Defguard tunnel interfaces are prefixed with `wg` by default.

**1. Create the config file:**

```sh
sudo nano /etc/NetworkManager/conf.d/90-defguard.conf
```

**2. Add the following content:**

```ini
[keyfile]
unmanaged-devices=interface-name:wg*
```

**3. Save and restart `NetworkManager`:**

```sh
sudo systemctl restart NetworkManager
```

The notification will no longer appear after this change.
