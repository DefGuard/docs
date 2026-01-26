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

We prepared a [git repository](https://github.com/DefGuard/deployment) with and example Docker Compose configuration.

To run your services using this example prepare your .env file by copying the template:

```bash
cp .env.template .env
```

Finally, run the service with Docker Compose:

```bash
docker compose up
```

Below you'll find a detailed breakdown of configuration for different components: Core, Proxy and Gateway.

## Deploying Core, database and reverse proxy services

Here is the docker-compose.yaml for the core and database. Configuration is split to the `.env` file (see below):

```
services:
  core:
    image: ghcr.io/defguard/defguard:latest
    restart: always
    container_name: "defguard"
    env_file: .env
    ports:
      # HTTP port - open on localhost, should be secured by reverse-proxy
      - "127.0.0.1:8000:8000"
      # gRPC port for gateway to connect to
      # open on all interfaces/IPs - whould be secured with custom CA (see .env)
      - "50055:50055"
    depends_on:
      - db
    volumes:
      # more info here:
      # https://docs.defguard.net/deployment-strategies/openid-rsa-key
      - ./rsakey.pem:/keys/rsakey.pem
      # more info about custom CA here:
      # https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
      - ./ca.pem:/keys/ca.pem

  db:
    image: postgres:17-alpine
    container_name: "defguard-db"
    env_file: .env
    volumes:
      - db:/var/lib/postgresql/data
      
volumes:
  db:
```

#### NGINX reverse-proxy

Now that you have core running, here is an example NGINX configuration to provide SSL termination:

```
upstream  defguard {
    server 127.0.0.1:8000;
}

server {
    listen 443 ssl http2;

    # your domain
    server_name defguard.secure-internal.net;

    access_log /var/log/nginx/defguard.log;
    error_log /var/log/nginx/defguard.error.log;

    ssl on;
    # we assume you already have Let'sEncrypt SSL certificates
    # for your domain
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

#### The configuration

Here is the `.env` file with all configuration variables:

```
# please generate each secret with:
# openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-64
DEFGUARD_SECRET_KEY=
DEFGUARD_AUTH_SECRET=
DEFGUARD_GATEWAY_SECRET=
DEFGUARD_YUBIBRIDGE_SECRET=

# if you plan to reverse-proxy defguard, please provide a full URL
# this URL will be shared in emails, enrollement messages, etc.:
DEFGUARD_URL=https://defguard.secure-internal.net
# Must be an effective domain of DEFGUARD_URL
# Changing DEFGUARD_WEBAUTHN_RP_ID will potentially break all your existing
# Webauthn credentials.
DEFGUARD_WEBAUTHN_RP_ID=defguard.secure-internal.net

# accepted: info/debug/warning/error
DEFGUARD_LOG_LEVEL=info

# more info about custom CA here:
# https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
DEFGUARD_PROXY_GRPC_CA=/keys/ca.pem
# gRPC URL of proxy (see proxy config)
DEFGUARD_PROXY_URL=https://proxy.host:50051
# more details about RSA key here:
# https://docs.defguard.net/deployment-strategies/openid-rsa-key
DEFGUARD_OPENID_KEY=rsakey.pem

# the URL of your proxy - will be displayed during enrollment, email
# messages or desktop client configuration
DEFGUARD_ENROLLMENT_URL=https://enrollment.public.net

# PostgreSQL database configuration for core
DEFGUARD_DB_HOST=db
DEFGUARD_DB_PORT=5432
DEFGUARD_DB_USER=defguard
# please generate password:
# openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-64
DEFGUARD_DB_PASSWORD=
DEFGUARD_DB_NAME=defguard

# database configuration for "db" container
# must be same as above
# database will be initialized with these values (the user/pass set here)
POSTGRES_DB=defguard
POSTGRES_USER=defguard
POSTGRES_PASSWORD=!SAME_AS-GENERATED-DEFGUARD_DB_PASSWORD!
```

## Deploying Proxy and reverse proxy service

Here is the docker-compose.yaml for the public proxy (enrollment service as well as desktop client configuration service).

To secure the gRPC communication, please generate the proxy CA and certificate, [more info here](grpc-ssl-communication.md#custom-ssl-ca-and-certificates).

```
proxy:
  image: ghcr.io/defguard/defguard-proxy:latest
  restart: unless-stopped
  ports:
     # HTTP port - should be secured by reverse proxy
     - "127.0.0.1:8080:8080"
     - "50051:50051"
  environment:
     # path in the volume to custom proxy cert & key
     - DEFGUARD_PROXY_GRPC_CERT=ca/proxy.crt
     - DEFGUARD_PROXY_GRPC_KEY=ca/proxy.key     
  volumes:
     - ./ca/proxy.crt:ca/proxy.crt
     - ./ca/proxy.key:ca/proxy.key
  
```

#### NGINX reverse-proxy

Now that you have proxy running, here is an example NGINX configuration to provide SSL termination:

```
upstream  defguard-proxy  {
	server   127.0.0.1:8080;
}

server {
	listen 443 http2;
	server_name enrollment.public.net;
	access_log /var/log/nginx/defguard-proxy.log;
	error_log /var/log/nginx/defguard-proxy.error.log;

        # we assume you already have Let'sEncrypt SSL certificates
        # for your domain
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

You'll need a token to deploy the Gateway service. You'll have to set it as DEFGUARD\_TOKEN environment variable. Details on how to obtain the token [here](gateway.md).

For gateway to control the WireGuard kernel as well as network, it's recommended to run in the _host_ network mode as well as there are needed some docker CAPs:

```
services:
  gateway: 
    image: ghcr.io/defguard/gateway:latest 
    restart: unless-stopped 
    network_mode: "host" 
    environment: 
      - DEFGUARD_GRPC_URL=https://core-ip:50055
      - DEFGUARD_GRPC_CA=/ca.pem
      - DEFGUARD_STATS_PERIOD=30
      # to get the token add a VPN location and get the token
      - DEFGUARD_TOKEN=tokenFromCoreLocation
      - DEFGUARD_GATEWAY_NAME=willBeVisibleInDefguardAsGWName
    volumes:
      # more info about custom CA here:
      # https://docs.defguard.net/deployment-strategies/grpc-ssl-communication#custom-ssl-ca-and-certificates
      - ./ca.pem:/ca.pem
    cap_add: 
      - NET_ADMIN 
```
