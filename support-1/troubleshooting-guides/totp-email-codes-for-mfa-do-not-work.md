# TOTP / email codes for MFA do not work

If TOTP codes (from an authenticator app) or email one-time codes are rejected during login or VPN connection, the most common cause is a clock skew on the server running Defguard core.

TOTP codes are time-based and are only valid within a short window (typically ±30 seconds). If the server clock is significantly out of sync with the client, valid codes will appear expired.

### Fix

Configure NTP time synchronisation on the server:

```sh
# Verify current time sync status
timedatectl status

# Enable and start systemd-timesyncd if not already active
sudo systemctl enable --now systemd-timesyncd
```

On systems using `chrony`:

```sh
sudo systemctl enable --now chronyd
chronyc tracking
```

After time synchronisation is active and the clock is accurate, retry the TOTP code. If the problem persists, regenerate the TOTP secret in the Defguard web UI and re-enroll the authenticator app.



### Client fix

Sometimes the problem is not with the core (server) but with client system. For that, follow instructions below for your operating system.

#### Linux

Check the current sync status:

```sh
timedatectl status
```

If `NTP service` shows as `inactive`, enable `systemd-timesyncd`:

```sh
sudo systemctl enable --now systemd-timesyncd
```

On distributions using `chrony` instead:

```sh
sudo systemctl enable --now chronyd
chronyc tracking
```

#### macOS

macOS uses `timed` for network time synchronisation. Verify it is enabled in **System Settings > General > Date & Time** and that **Set time and date automatically** is on.

To force an immediate sync from the command line:

```sh
sudo sntp -sS time.apple.com
```

#### Windows

Open **Settings > Time & Language > Date & Time**. Confirm **Set time automatically** is on and click **Sync now** to force an immediate update.

Alternatively from PowerShell ( as Administrator):

```powershell
w32tm /resync /force
```
