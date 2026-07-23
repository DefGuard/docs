# Client crashes immediately on launch with "Error 71 (Protocol error) dispatching to Wayland display"

{% hint style="info" %}
This issue has been resolved in v2.1. Defguard client now automatically sets appropriate environment variables to prevent webkitgtk errors. Please upgrade to the latest version.
{% endhint %}

### Symptoms

The Defguard Client window never appears and the process exits immediately after launch. Running it from a terminal shows an error similar to:

```
Gdk-Message: Error 71 (Protocol error) dispatching to Wayland display.
```

This has been observed on Wayland sessions across multiple distributions and desktop environments, for example:

* Fedora 44, KDE Plasma 6.7.2

### Cause

Defguard Client is built with [Tauri](https://tauri.app/), which on Linux renders its UI using the system-provided **WebKitGTK** webview rather than a bundled browser engine. Recent versions of WebKitGTK default to a **DMA-BUF renderer**, which allocates GPU buffers through GBM (Generic Buffer Management) for hardware-accelerated rendering.

On some GPU driver and compositor combinations (most commonly with the proprietary **NVIDIA** driver on a Wayland session, but occasionally with other configurations as well) the driver does not support all the buffer formats or modifiers that WebKitGTK requests. Buffer allocation then fails, the Wayland compositor tears down the client's protocol connection, and GTK reports this as `Error 71`, causing the application to close instantly.

This is a known, upstream WebKitGTK/Tauri issue that affects many Tauri-based applications on Linux, not something specific to the Defguard Client itself. It is documented by the Tauri project here: [Linux Graphics Issues](https://v2.tauri.app/develop/debug/linux-graphics/).

### Workaround

Disable the DMA-BUF renderer so WebKitGTK falls back to shared-memory buffers instead of GBM:

```bash
WEBKIT_DISABLE_DMABUF_RENDERER=1 /usr/bin/defguard-client
```

This trades away the faster, hardware-accelerated rendering path for a slower but reliable one. For most users the difference is not noticeable, since the Defguard Client UI is not graphics-intensive.

#### Making the workaround permanent

Rather than typing the variable every time, set it in the application's `.desktop` launcher so it applies whenever the client is started from a menu or application launcher.

Copy the desktop entry to your user directory if you haven't already:

```bash
mkdir -p ~/.local/share/applications
cp /usr/share/applications/defguard-client.desktop ~/.local/share/applications/
```

Edit the `Exec` line in the copied file:

```ini
Exec=env WEBKIT_DISABLE_DMABUF_RENDERER=1 /usr/bin/defguard-client
```

#### Alternative: try `__NV_DISABLE_EXPLICIT_SYNC=1` first (NVIDIA only)

On NVIDIA systems, this narrower workaround sometimes resolves the crash without giving up hardware acceleration:

```bash
__NV_DISABLE_EXPLICIT_SYNC=1 /usr/bin/defguard-client
```

If this alone fixes the issue, prefer it over `WEBKIT_DISABLE_DMABUF_RENDERER=1`, since it only disables the newer explicit-sync path rather than the entire accelerated rendering pipeline.

### Reporting this issue

If neither workaround resolves the crash, please open an issue and include:

* Output of `WAYLAND_DEBUG=1 defguard-client` (or the terminal output at minimum)
* GPU vendor and driver version (`nvidia-smi` or equivalent)
* Distribution, desktop environment, and Wayland/X11 session type
* Whether `WEBKIT_DISABLE_DMABUF_RENDERER=1` and `__NV_DISABLE_EXPLICIT_SYNC=1` were tested, and the result of each
