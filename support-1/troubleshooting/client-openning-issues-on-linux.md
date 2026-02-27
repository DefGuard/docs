# Client openning issues on Linux

If the application opens to a **blank white window** or **crashes immediately**, it is likely due to a rendering conflict with the WebKit engine's hardware acceleration.

### Symptoms

* The application window opens but remains entirely white.
* The process terminates (crashing) immediately after the window appears.
* Logs indicate errors related to `Compositing` or `GPU acceleration`.

### Solution

Setting the `WEBKIT_DISABLE_COMPOSITING_MODE` environment variable to `1` forces the client to use a more stable rendering path.

Also in certain cases `WEBKIT_DMABUF_RENDERER_DISABLE_GBM` to `1` is also required.

