---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/docker-compose
---

# Docker Compose

## Introduction

This document provides a complete example of how to deploy Defguard using Docker Compose, including configuration for all components – Core, Edge, and Gateway. It covers Docker image tags, environment variables, and reverse-proxy setup examples to help you quickly launch a fully functional Defguard environment.

We recommend deploying each Defguard service on a dedicated server or virtual machine to ensure better isolation, performance, and security. In this setup, each Docker Compose file should be used for a single service, keeping the Core, Edge, and Gateway components physically separated.

{% hint style="info" %}
Please note that we also offer Docker Compose deployment with [_one-line quick deployment_](../getting-started/one-line-install.md)_,_ but this method is recommended for PoC/quick deployment as **it launches everything on one server and all services in one docker compose**.
{% endhint %}

## Docker images and tags

We use `latest` (latest production images) tags in the examples below, but other tags, like full release versions (for example, `2.0.0`) can be used as well.

All docker images for Core, Gateway, and Edge have these additional tags:

* `latest` - the latest stable production release.
* `vX.Y`, `vX.Y.Z`, `vX.Y-alpha1` - fixed tags for specific stable and alpha releases.
* `pre-release`- the latest pre-production release (equivalent to vX.Y-alpha1).
* `dev` - the latest development build from the dev branch (experimental).

{% hint style="warning" %}
We recommend always using fixed, stable tags (`vX.Y`, `vX.Y.Z`) for your production deployment.
{% endhint %}

All available Docker images are available in the links below:

* [Core images](https://github.com/DefGuard/defguard/pkgs/container/defguard)
* [Gateway images](https://github.com/DefGuard/defguard/pkgs/container/gateway)
* [Edge images](https://github.com/DefGuard/defguard/pkgs/container/defguard-proxy)

## Example Docker Compose deployment repository

We prepared a dedicated [git repository](https://github.com/DefGuard/deployment) with and example Docker Compose configuration files.

## Deploying Core and PostgreSQL services

Here is the **docker-compose.yaml** file for Defguard Core and PostgreSQL database. Configuration is split into the `.env` file (see below):

```yaml
services:
  core:
    image: ghcr.io/defguard/defguard
    logging:
      driver: journald
      options:
        tag: "defguard-core"
    environment:
      DEFGUARD_COOKIE_INSECURE: "true"
      DEFGUARD_DB_HOST: db
      DEFGUARD_DB_PORT: 5432
      DEFGUARD_DB_USER: defguard
      DEFGUARD_DB_PASSWORD: defguard
      DEFGUARD_DB_NAME: defguard
    depends_on:
      - db
    ports:
      - "8000:8000"

  db:
    image: postgres:18-alpine
    logging:
      driver: journald
      options:
        tag: "defguard-db"
    environment:
      POSTGRES_DB: defguard
      POSTGRES_USER: defguard
      POSTGRES_PASSWORD: defguard
    volumes:
      - ./.volumes/db:/var/lib/postgresql
```

To run the service with Docker Compose, execute this command:

```sh
docker compose up
```

#### Optional nginx reverse-proxy

Depending on your infrastructure, you may choose to keep the setup simple and let Defguard handle SSL termination for you. Learn more about this functionality [here](../tutorials/initial-setup-wizard-setting-up-from-scratch.md#configure-ssl-for-core). In that case skip stis step.

Alternatively, you can place a reverse proxy in front of your Core service to manage SSL termination.

Here is an example [nginx](https://nginx.org/) configuration to provide SSL termination:

```
upstream defguard {
  server 127.0.0.1:8000;
}

server {
  listen 443 ssl http2;

  # change this to a desired domain
  server_name defguard.secure-internal.net;

  access_log /var/log/nginx/defguard.log;
  error_log /var/log/nginx/defguard.error.log;

  ssl on;
  # assuming Let’s Encrypt SSL certificates exists for the domain
  ssl_certificate /etc/letsencrypt/live/secure-internal.net/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/secure-internal.net/privkey.pem;

  client_max_body_size 20m;

  location / {
    proxy_connect_timeout 300;
    proxy_pass http://defguard;
    proxy_set_header Connection "upgrade";
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header X-Forwarded-for $remote_addr;
  }
}
```

## Deploying Edge

Here is the **docker-compose.yaml** file for Defguard Edge.

```yaml
services:
  edge:
    image: ghcr.io/defguard/defguard-proxy
    logging:
      driver: journald
      options:
        tag: "defguard-edge"
    volumes:
      - ./.volumes/certs/edge:/etc/defguard/certs
    ports:
      - "8080:8080"
      - "80:80"
      - "443:443"
```

#### Optional nginx reverse-proxy

Depending on your infrastructure, you may choose to keep the setup simple and let Defguard handle SSL termination for you. Learn more about this functionality [here](../tutorials/initial-setup-wizard-setting-up-from-scratch.md#configure-ssl-for-edge). In that case skip stis step.

Alternatively, you can place a reverse proxy in front of your Edge service to manage SSL termination.

Here is an example [nginx](https://nginx.org/) configuration to provide SSL termination:

```
upstream defguard-proxy  {
	server 127.0.0.1:8080;
}

server {
	listen 443 http2;
  # change this to a desired domain
	server_name enrollment.public.net;
	access_log /var/log/nginx/defguard-proxy.log;
	error_log /var/log/nginx/defguard-proxy.error.log;

  # assuming Let’s Encrypt SSL certificates exists for the domain
	ssl_certificate /etc/letsencrypt/live/public.net/fullchain.pem;
	ssl_certificate_key /etc/letsencrypt/live/public.net/privkey.pem;

	client_max_body_size 20m;

  location / {
    proxy_pass         http://defguard-proxy;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
  }
}
```

## Deploying Gateway service

Here is the **docker-compose.yaml** file for Defguard Gateway.

```yaml
services:
  gateway:
    image: ghcr.io/defguard/gateway
    logging:
      driver: journald
      options:
        tag: "defguard-gateway"
    cap_add:
      - NET_ADMIN
    volumes:
      - ./.volumes/certs/gateway:/etc/defguard/certs
    ports:
      - "51820:51820/udp"
    environment:
      DEFGUARD_STATS_PERIOD: 10
      HEALTH_PORT: 55003
```

## Persisting logs

By default, Docker containers' logs live only in Docker's own log storage and are lost on host reboot or log rotation. To persist them, each service's compose config sets the `journald` logging driver with a distinct `tag`, forwarding container logs to the host's systemd journal:

```yaml
logging:
  driver: journald
  options:
    tag: "defguard-core"
```

{% hint style="info" %}
`journald` requires the host to run `systemd` with a running journal service. This is the default on most Linux distributions.
{% endhint %}

Query logs for a given service with `journalctl`, filtering by tag:

```sh
journalctl -t defguard-core -f
```

## Upgrading

{% hint style="warning" %}
**Always back up your database before upgrading Core.** See the [deployment overview](overview.md#backup) for instructions.
{% endhint %}

1. **Review the migration guides** for breaking changes that may affect your setup. See [migration guides](upgrading.md).
2.  **Update the image tags** in your `docker-compose.yaml` files for each component:<br>

    ```yaml
    # Core:
    image: ghcr.io/defguard/defguard:<CORE_TAG>

    # Edge:
    image: ghcr.io/defguard/defguard-proxy:<EDGE_TAG>

    # Gateway:
    image: ghcr.io/defguard/gateway:<GATEWAY_TAG>

    ```


3.  Pull the new images and restart:<br>

    ```bash
    docker compose pull
    docker compose down
    docker compose up -d
    ```
4.  Verify all services are up:<br>

    ```bash
    docker compose ps
    ```

### Downgrading

{% hint style="danger" %}
Downgrading after a database migration has run is not trivial and may require restoring from a backup. Database migrations are applied automatically when Core starts. Always back up before upgrading.
{% endhint %}
