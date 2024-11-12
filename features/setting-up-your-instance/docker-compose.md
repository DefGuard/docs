# Docker Compose

Here are basic and simple docker-compose configuration files that will enable you to quickly deploy your own instance manually. We also assume in this example, that all services will deployed on dedicated servers/VMs - separating them physically, thus each compose is for a separate service.

{% hint style="success" %}
Please not that we also offer docker-compose deployment with [_one-line quick deployment_](one-line-install.md)_,_ but this method is recommended for PoC/quick deployment as **it launches everything on one server and all services in one docker compose**.
{% endhint %}

## Core

Here is the docker-compose.yaml for the core and database. Configuration is split to the `.env` file (see below):

```
version: "3"
services:
  core:
    image: ghcr.io/defguard/defguard:latest
    restart: always
    container_name: "defguard"
    env_file: .env
    ports:
      # HTTP port
      - "80000:8000"
      # gRPC port for gateway to connect to
      - "50055:50055"
    depends_on:
      - db
    volumes:
      # more info here:
      # https://docs.defguard.net/admin-and-features/setting-up-your-instance/openid-rsa-key
      - ./rsakey.pem:/keys/rsakey.pem
      # more info about custom CA here:
      # https://docs.defguard.net/admin-and-features/setting-up-your-instance/grpc-ssl-communication#custom-ssl-ca-and-certificates
      - ./ca.pem:/keys/ca.pem

  db:
    image: postgres:17-alpine
    container_name: "defguard-db"
    env_file: .env
    volumes:
      - db:/var/lib/postgresql/data
```

### The configuration

Here is the `.env` file with all configuration variables:

```
# please generate each secret with:
# openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-63
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
# https://docs.defguard.net/admin-and-features/setting-up-your-instance/grpc-ssl-communication#custom-ssl-ca-and-certificates
DEFGUARD_PROXY_GRPC_CA=/keys/ca.pem
# gRPC URL of proxy (see proxy config)
DEFGUARD_PROXY_URL=https://proxy.host:50051
# more details about RSA key here:
# https://docs.defguard.net/admin-and-features/setting-up-your-instance/openid-rsa-key
DEFGUARD_OPENID_KEY=rsakey.pem

# the URL of your proxy - will be displayed during enrollment, email
# messages or desktop client configuration
DEFGUARD_ENROLLMENT_URL=https://enrollment.public.net

# PostgreSQL database configuration for core
DEFGUARD_DB_HOST=db
DEFGUARD_DB_PORT=5432
DEFGUARD_DB_USER=defguard
# please generate password:
# openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-63
DEFGUARD_DB_PASSWORD=
DEFGUARD_DB_NAME=defguard

# database configuration for "db" container
# must be same as above
# database will be initialized with these values (the user/pass set here)
POSTGRES_DB=defguard
POSTGRES_USER=defguard
POSTGRES_PASSWORD=!SAME_AS-GENERATED-DEFGUARD_DB_PASSWORD!
```

