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

#### nginx reverse-proxy

Now that you have core running, here is an example [nginx](https://nginx.org/) configuration to provide SSL termination:

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

## Deploying Edge and reverse proxy service

Here is the **docker-compose.yaml** file for Defguard Edge.

```yaml
services:
  edge:
    image: ghcr.io/defguard/defguard-proxy
    volumes:
      - ./.volumes/certs/edge:/etc/defguard/certs
    ports:
      - "8080:8080"
      - "80:80"
      - "443:443"
```

#### nginx reverse-proxy

Now that you have proxy running, here is an example [nginx](https://nginx.org/) configuration to provide SSL termination:

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
