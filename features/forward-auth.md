# Forward auth

Defguard supports [forward auth](https://app.gitbook.com/o/Z3mGSAbEj9iLdZ7cNFlL/s/hM5HQk0xXl585Dm4YMUV/\~/changes/48/features/forward-auth) integration with popular reverse proxies (tested with [traefik](https://doc.traefik.io/traefik/) and [caddy](https://caddyserver.com/)). This allows you to use defguard to secure services which don't provide their own authorization or OAuth integration.

{% hint style="warning" %}
In order for forward auth to work the services you are trying to protect must be available at URLs within the same base domain as your defguard instance.

For example if you are serving your defguard UI at `id.yourdomain.com`, then your services must use other subdomains of `yourdomain.com`, e.g. ``app1.yourdomain.com, `service.yourdomain.com` etc``.

Additionally you have to update your [defguard config](../in-depth/environmental-variables-configuration.md#auth-cookies-configuration) to set the cookies domain to `yourdomain.com`.
{% endhint %}

## Example configurations

For brevity all of the examples below assume you are hosting your defguard instance at `defguard.yourdomain.com`.

We'll use a basic [whoami](https://github.com/traefik/whoami) container as an example service which will be available at `whoami.yourdomain.com`.

### Traefik

#### docker-compose.yml

```yaml
version: "3"

services:
  traefik:
    image: traefik:v2.9
    command: --api.insecure=true --providers.docker
    ports:
      - "80:80" # HTTP port
      - "8080:8080" # Web UI port
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
  whoami:
    image: traefik/whoami
    labels:
      - "traefik.http.routers.whoami.rule=Host(`whoami.yourdomain.com`)"
      - "traefik.http.middlewares.defguardauth.forwardauth.address=http://defguard.yourdomain.com/api/v1/forward_auth"
      - "traefik.http.routers.whoami.middlewares=defguardauth"
```

### Caddy

#### Caddyfile

```
# Disable HTTPS for this example (WARNING: Do not use in production)
{
    auto_https off
    https_port 80
}

whoami.yourdomain.com {
    forward_auth defguard.yourdomain.com {
      uri /api/v1/forward_auth
    }
    reverse_proxy whoami:80
}
```

#### docker-compose.yml

```yaml
version: "3"

services:
  caddy:
    image: caddy:2.6.4-alpine
    ports:
      - "80:80" # HTTP port
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
  whoami:
    image: traefik/whoami
```
