# Docker Compose

## Introduction

This document provides a complete example of how to deploy Defguard using Docker Compose, including configuration for all components - Core, Proxy, and Gateway. It covers Docker image tags, environment variables, and reverse-proxy setup examples to help you quickly launch a fully functional Defguard environment.

We recommend deploying each Defguard service on a dedicated server or virtual machine to ensure better isolation, performance, and security. In this setup, each Docker Compose file should be used for a single service, keeping the Core, Proxy, and Gateway components physically separated.

{% hint style="info" %}
Please note that we also offer docker-compose deployment with [_one-line quick deployment_](../getting-started/one-line-install.md)_,_ but this method is recommended for PoC/quick deployment as **it launches everything on one server and all services in one docker compose**.
{% endhint %}

## Docker images and tags

We use `latest` (latest production images) tags in the examples below, but you can use others.

All docker images for Core, Gateway, and Proxy have these additional tags:

* `latest` - the latest stable production release.
* `vX.Y`, `vX.Y.Z`, `vX.Y-alpha1` - fixed tags for specific stable and alpha releases.
* `pre-release`- the latest pre-production release (equivalent to vX.Y-alpha1).
* `dev` - the latest development build from the dev branch (experimental).

{% hint style="warning" %}
We recommend always using fixed, stable tags (`vX.Y`, `vX.Y.Z`) for your production deployment.
{% endhint %}

## Example Docker Compose deployment repository

We prepared a [git repository](https://github.com/DefGuard/deployment) with and example Docker Compose configuration. The deployment files located in the `docker-compose-segmented` directory are split into 3 separate Docker Compose, one for each component: Core, Proxy, Gateway.

Before running any of the services, make sure to make appropriate changes either in the `.env` file or the `environment` section of the compose for every component.&#x20;

To run one of the components, simply do:

```bash
docker compose up
```

Below you'll find a detailed breakdown of configuration for different components: Core, Proxy and Gateway.

We recommend following the guide on [grpc-ssl-communication.md](grpc-ssl-communication.md "mention") to further secure the communication between components.

## Deploying Core, database and reverse proxy services

Here is the `compose.yml` for the Core, Database and a reverse proxy (Nginx Proxy Manager). Configuration is split to the `.env` file (see below).

```yml
services:
  core:
    image: ghcr.io/defguard/defguard:latest
    restart: unless-stopped
    container_name: "defguard"
    env_file: .env
    ports:
      # gRPC port for the Gateway to connect to
      # open on all interfaces/IPs - should be secured with custom CA (see .env)
      - "50055:50055"
    depends_on:
      db:
        condition: service_healthy
    # volumes:
    # More info here:
    # https://docs.defguard.net/deployment-strategies/openid-rsa-key
    # - ./rsakey.pem:/keys/rsakey.pem
    # More info about securing gRPC communication here:
    # https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
    # - ./core.pem:/certs/core.pem
    # - ./core.key:/certs/core.key
    # - ./ca.pem:/certs/ca.pem

  db:
    image: postgres:17-alpine
    restart: unless-stopped
    container_name: "defguard-db"
    env_file: .env
    volumes:
      - ./volumes/db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U defguard"]
      interval: 5s
      timeout: 5s
      retries: 5

  npm:
    image: "jc21/nginx-proxy-manager:latest"
    restart: unless-stopped

    ports:
      - "80:80" # HTTP Port
      - "443:443" # HTTPS Port
      - "81:81" # Admin Web Port

    environment:
      TZ: "Europe/Warsaw"

    volumes:
      - ./volumes/npm/data:/data
      - ./volumes/npm/letsencrypt:/etc/letsencrypt

```

#### Nginx Proxy Manager

The Reverse Proxy should be setup to access Defguard Core dashboard. The Nginx Proxy Manager (NPM) can be configured by accessing the web interface at port 81.

The Nginx proxy should be configured to proxy the traffic to: `http://core:8000`.&#x20;

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

We recommend also setting up certificates, to serve the traffic over HTTPS. Since Core is designed to be deployed in an internal network, we recommend selecting the DNS validation in NPM or uploading custom, already issued certificates.

<figure><img src="../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

#### The configuration

Here is the `.env` file with all configuration variables:

```dotenv
# You can generate each secret with:
# openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-64
## General Core configuration ##
DEFGUARD_AUTH_SECRET=<64_CHAR_RANDOM_SECRET>
DEFGUARD_YUBIBRIDGE_SECRET=<64_CHAR_RANDOM_SECRET>
DEFGUARD_GATEWAY_SECRET=<64_CHAR_RANDOM_SECRET>
DEFGUARD_SECRET_KEY=<64_CHAR_RANDOM_SECRET>
DEFGUARD_URL=<YOUR_DEFGUARD_CORE_URL>
# DEFGUARD_WEBAUTHN_RP_ID=<YOUR_DEFGUARD_WEBAUTHN_RP_ID>
# More details about RSA key here:
# https://docs.defguard.net/deployment-strategies/openid-rsa-key
# DEFGUARD_OPENID_KEY=rsakey.pem
# Accepted values: debug, info, warn, error
DEFGUARD_LOG_LEVEL=info

## Proxy/Enrollment configuration ##
DEFGUARD_ENROLLMENT_URL=<YOUR_DEFGUARD_ENROLLMENT_URL> # The URL of your Proxy - will be displayed during enrollment, email messages or desktop client configuration
DEFGUARD_PROXY_URL=<YOUR_DEFGUARD_PROXY_GRPC_URL>
# DEFGUARD_COOKIE_INSECURE=true  # If you are accessing the dashboard via HTTP instead of HTTPS, set this to true.

## GRPC Certificate configuration ##
# More info about securing gRPC communication here: https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
# DEFGUARD_GRPC_CERT=/certs/core.pem
# DEFGUARD_GRPC_KEY=/certs/core.key
# DEFGUARD_PROXY_GRPC_CA=/certs/ca.pem

## Database configuration ##
# For the database container
POSTGRES_DB=defguard
POSTGRES_USER=defguard
POSTGRES_PASSWORD=<YOUR_DB_PASSWORD>
# For the Core container
DEFGUARD_DB_HOST=db
DEFGUARD_DB_PORT=5432
DEFGUARD_DB_PASSWORD=<YOUR_DB_PASSWORD>
DEFGUARD_DB_USER=defguard
DEFGUARD_DB_NAME=defguard

```

## Deploying Proxy and reverse proxy service

Here is the `compose.yml` for Defguard Proxy (enrollment and desktop client configuration service).

```yml
services:
  proxy:
    image: ghcr.io/defguard/defguard-proxy:latest
    restart: unless-stopped
    container_name: "defguard-proxy"
    ports:
      - "50051:50051"
    environment:
      - DEFGUARD_PROXY_URL=<PROXY_GRPC_URL>
      - DEFGUARD_LOG_LEVEL=info
      # More info about securing gRPC communication here:
      # https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
      #- DEFGUARD_PROXY_GRPC_CERT=/certs/proxy.pem
      #- DEFGUARD_PROXY_GRPC_KEY=/certs/proxy.key
    # volumes:
    #   - ./proxy.pem:/certs/proxy.pem
    #   - ./proxy.key:/certs/proxy.key
  npm:
    image: "jc21/nginx-proxy-manager:latest"
    restart: unless-stopped
    ports:
      - "80:80" # HTTP Port
      - "443:443" # HTTPS Port
      - "81:81" # Admin Web Port

    environment:
      TZ: "Europe/Warsaw"

    volumes:
      - ./volumes/npm/data:/data
      - ./volumes/npm/letsencrypt:/etc/letsencrypt

```

#### Nginx Proxy Manager

The Reverse Proxy should be setup to access Defguard Proxy interface and allow serving all traffic destined to it via HTTPS. The Nginx Proxy Manager (NPM) can be configured by accessing the web interface at port 81.

The Nginx proxy should be configured to proxy the traffic to: `http://proxy:8080`.&#x20;

Make sure to turn on websockets supports, as some Defguard features rely on it.

<figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

NPM can automatically issue certificates for your Defguard Proxy, by performing a domain validation using port 80. Make sure this port is open when trying to configure certificates. Alternatively, DNS validation can be used.

<figure><img src="../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>

## Deploying Gateway service

You'll need a token to deploy Defguard Gateway. You'll have to set it as `DEFGUARD_TOKEN` environment variable. Details on how to obtain the token [here](gateway.md).

For Gateway to control the WireGuard kernel as well as network, it's recommended to run in the **host** network mode and adding the `NET_ADMIN` capability.

```yml
services:
  gateway:
    image: ghcr.io/defguard/gateway:latest
    restart: unless-stopped
    container_name: "defguard-gateway"
    network_mode: "host"
    environment:
      - DEFGUARD_GRPC_URL=<CORE_GRPC_URL>
      - DEFGUARD_TOKEN=<GATEWAY_TOKEN>
      - DEFGUARD_LOG_LEVEL=info
      # More info about securing gRPC communication here:
      # https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
      # - DEFGUARD_GRPC_CA=/ca.pem
    # volumes:
    #   - ./ca.pem:/ca.pem
    cap_add:
      - NET_ADMIN
```
