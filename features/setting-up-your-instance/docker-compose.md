# Deploy Defguard with docker compose

We prepared a [git repository](https://github.com/DefGuard/deployment) with docker-compose configuration, clone it:

```
git clone git@github.com:DefGuard/deployment.git && cd deployment
```

In docker-compose directory you'll find a template env file called `.env.template`. Copy it:

```
cd docker-compose
cp .env.template .env
```

And then edit the values in `.env` file to setup your secrets. Those should be kept... well, secret.

> You can generate random strings for secrets with e.g.: `openssl rand -base64 30`

Once that's done you can start the stack with:

```
docker-compose up
```

> Make sure you have [Docker](https://www.docker.com/get-started/) and [Docker Compose](https://docs.docker.com/compose/install/) installed.

That's it, Defguard should be running on port 80 of your server ([http://localhost](http://localhost) if you're running locally).

## OpenID RSA setup

By default Defguard uses [HMAC](https://en.wikipedia.org/wiki/HMAC) algorithm for OIDC token validation.
If you want to use [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)), you'll have to:

1. Create volume directory:

```
mkdir -p .volumes/core
```

2. Generate RSA keys:

```
openssl genpkey -out .volumes/core/rsakey.pem -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

3. Add environment variable and mount the volume in docker-compose core service

```yaml
core:
  ...
  environment:
    ...
    DEFGUARD_OPENID_KEY: /keys/rsakey.pem
  ...
  volumes:
    - ./.volumes/core/rsakey.pem:/keys/rsakey.pem
```
