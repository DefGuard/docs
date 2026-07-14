# Windows client unable to connect

If the Windows client shows command timeout errors or fails to establish a connection, check the background service logs first. Open **Settings** in the client and review the log output, or locate the [#log-files](../../../using-defguard-for-end-users/desktop-client/#log-files "mention") directly.

### Stale WireGuard tunnel

If the background service logs contain errors like `Unable to access interface: No such file or directory`, a previous tunnel was not shut down cleanly. This leaves a ghost tunnel entry that blocks new connections.

To fix it:

1. Open the Windows **Services** manager (`services.msc`).
2. Look for any services named **WireGuard Tunnel**.
3. Stop and delete any such services.
4. Retry the connection in Defguard.

<figure><img src="../../../.gitbook/assets/image (273).png" alt=""><figcaption></figcaption></figure>
