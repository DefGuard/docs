# Docker Compose

securelyWe prepared a [git repository](https://github.com/DefGuard/deployment) with docker-compose configuration, clone it:

```
git clone https://github.com/DefGuard/deployment.git && cd deployment
```

In docker-compose directory you'll find a template env file called `.env.template`. Copy it:

```
cd docker-compose
cp .env.template .env
```

And then edit the values in `.env` file to setup your secrets. Those should be kept... well, secret.

{% hint style="info" %}
You can generate random strings for secrets with e.g.:

`openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-63`
{% endhint %}

## SSL setup

{% hint style="warning" %}
It's crytically important to ensure SSL encryption between Defguard and gRPC client services (e.g. Gateway and Enrollment services). You should only skip this step if you plan to have a reverse proxy in between that adds encryption itself.
{% endhint %}

You'll need a valid:

* CA certificate (defguard-ca.pem) - used by gRPC clients for Defguard identity verification & encryption
* Certificate for Defguard signed by CA (defguard.crt) - used by Defguard to serve gRPC services
* Private key for Defguard (defguard.key) - used by Defguard for encryption

[Here](https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/) is a good tutorial on how to generate a self-signed certificate.

Put the certificates in .volumes/ssl directory.

Once that's done you can start the stack with:

```
docker-compose up
```

{% hint style="info" %}
Make sure you have [Docker](https://www.docker.com/get-started/) and [Docker Compose](https://docs.docker.com/compose/install/) installed.
{% endhint %}

That's it, Defguard should be running on port 80 of your server ([http://localhost](http://localhost) if you're running locally).

## OpenID RSA setup

By default Defguard uses [HMAC](https://en.wikipedia.org/wiki/HMAC) algorithm for OIDC token validation. If you want to use [RSA](https://en.wikipedia.org/wiki/RSA\_\(cryptosystem\)), you'll have to:

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

## LDAP

To setup LDAP integration, you'll have to configure environment variables for core service. You'll find more info on how to do this [here](../ldap-synchronization-setup.md).
