# Docker Compose

## One-line install script

To simplify the setup and enable automated deployment we prepared a script which will deploy a complete defguard instance, including an enrollment proxy and VPN gateway. To use it just create a working directory set up [configuration options](docker-compose.md#configuration) and run a following command:

```shell
curl --proto '=https' --tlsv1.2 -sSf -L https://raw.githubusercontent.com/DefGuard/deployment/main/docker-compose/setup.sh -O && bash setup.sh
```

If you provide all required configuration options after the script finishes you should have a fully functional defguard instance with an enrollment proxy and VPN gateway to connect wireguard clients to.

{% hint style="warning" %}
The instance deployed by the script is meant to serve as a starting point and makes some tradeoffs to enable automated setup. Most importantly it assumes that your Web UI is available publicly (to generate SSL certificates with Caddy). In general it's not recommended and we strongly encourage you to customize this setup to work better within your own infrastructure.
{% endhint %}

Of course if you feel rightly uneasy about running random shell scripts from the internet feel free to inspect the [source code](https://raw.githubusercontent.com/DefGuard/deployment/main/docker-compose/setup.sh).

The script does the following:

* reads configuration from environment variables, `.env` file or user input
* prepares a docker-compose file
* prepares an `.env` file for the compose stack
* creates a `.volumes` directory for persistent storage
* generates secret keys and certificates
* sets up an initial VPN location and VPN gateway
* starts the compose stack

### Prerequisites

In order to work the script requires some specific tools to be available and also some infrastructure-level settings to be pre-configured.

#### Tools

* `bash`
* `openssl`
* `curl`
* `sed`
* `grep`
* `docker`
* `docker-compose` - not necessary if using newer Docker versions (20.10+) which include the `docker compose` command

#### Environment setup

* server has a public IP address
* public DNS records for your chosen domain
* allow Docker to bind on host ports 80 and 443; sometimes this requires setting the `net.ipv4.ip_unprivileged_port_start` sysctl variable to 80
* enable IP forwarding (`sysctl -w net.ipv4.ip_forward=1`)
* firewall rules
  * allow incoming traffic on chosen Wireguard port and port 443
  * enable `MASQUERADE` for VPN traffic (for example `iptables -t nat -I POSTROUTING 1 -s {vpn_subnet} -o {internet_interface} -j MASQUERADE`)

### Configuration

There are several options that can be configured to customize your defguard instance. They can be provided to the script in following ways:

* by setting environment variables in your shell
* by providing an `.env` file in the working directory
* by running the script manually and setting CLI options
* by providing user input

#### Environment variables

* `DEFGUARD_DOMAIN` - domain for you defguard web UI (e.g. `id.example.com`)
* `DEFGUARD_ENROLLMENT_DOMAIN` - (optional) domain for the enrollment service; if not set the service will not be deployed
* `DEFGUARD_USE_HTTPS` - (optional) set to any value if you want Caddy to generate SSL certificates and use HTTPS
* `DEFGUARD_VPN_NAME`- (optional) name of initial VPN location to create; if not provided the script will not set up the VPN gateway
* `DEFGUARD_VPN_IP`- (optional if VPN name not set) gateway address within the VPN network (e.g. `10.0.50.1/24`)
* `DEFGUARD_VPN_GATEWAY_IP`- (optional if VPN name not set) gateway public IP
* `DEFGUARD_VPN_GATEWAY_PORT`- (optional if VPN name not set) gateway public port
* `CORE_IMAGE_TAG`- (optional) tag to use for `defguard` Docker image&#x20;
* `PROXY_IMAGE_TAG`- (optional) tag to use for `defguard-proxy` Docker image&#x20;
* `GATEWAY_IMAGE_TAG`- (optional) tag to use for `defguard-gateway` Docker image

#### CLI options

```
defguard deployment setup script v0.1.0
Copyright (C) 2023 teonite <https://teonite.com>

Usage:  [options]

Available options:

        --help                         this help message
        --non-interactive              run in non-interactive mode (no user input)
        --domain <domain>              domain where defguard web UI will be available
        --enrollment-domain <domain>   domain where enrollment service will be available
        --use-https                    configure reverse proxy to use HTTPS
        --vpn-name <name>              VPN location name
        --vpn-ip <address>             VPN server address & netmask (e.g. 10.0.50.1/24)
        --vpn-gateway-ip <ip>          VPN gateway external IP
        --vpn-gateway-port <port>      VPN gateway external port
```

## Manual install

If you prefer to customize your installation manually, we prepared a [git repository](https://github.com/DefGuard/deployment) with docker-compose configuration, clone it:

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
