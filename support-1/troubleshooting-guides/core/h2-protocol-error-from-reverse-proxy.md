# h2 protocol error from reverse proxy

Error message:

```
h2 protocol error: http2 error: stream error received: not a result of an error
```

This error appears when the client maintains a long-lived connection through a reverse proxy (nginx, Caddy, HAProxy, etc.) and the proxy closes the connection due to its idle or keepalive timeout.

This is expected behaviour. Every reverse proxy has a maximum time it will keep an idle connection open. Once that threshold is reached, the proxy closes the stream, which surfaces as this h2 error on the client side.

Increasing the proxy's keepalive or idle timeout reduces how frequently the error appears, but it will still occur eventually. The client handles reconnection automatically.

No action is required on the Defguard side. The errors are benign and can be ignored.
