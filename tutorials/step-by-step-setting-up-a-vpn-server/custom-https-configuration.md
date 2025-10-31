# Custom HTTPS Configuration

By default the install script generates a minimal Caddyfile:

```
https://defguard.example.com {
    reverse_proxy core:8000
}

https://proxy.example.com {
    reverse_proxy proxy:8080
}

:80 {
    respond 404
}
:443 {
    respond 404
}
```

This configuration leverages Caddy's default [automatic HTTPS support](https://caddyserver.com/docs/automatic-https).

If you wish to customize your HTTPS certificate setup, please follow the official [Caddy docs](https://caddyserver.com/docs/caddyfile/options#tls-options).
