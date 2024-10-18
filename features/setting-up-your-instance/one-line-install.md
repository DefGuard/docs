# One-line install script

To simplify the setup and enable automated deployment we prepared a script which will deploy a complete defguard instance, including an enrollment proxy and VPN gateway. To use it just create a working directory set up [configuration options](one-line-install.md#configuration) and run one of the following commands (depending on which version you'd like to use):

**Production release (most stable)**

```shell
curl --proto '=https' --tlsv1.2 -sSf -L https://raw.githubusercontent.com/DefGuard/deployment/main/docker-compose/setup.sh -O && bash setup.sh
```

**Pre-release**

```bash
curl --proto '=https' --tlsv1.2 -sSf -L https://raw.githubusercontent.com/DefGuard/deployment/main/docker-compose/setup.sh -O && bash setup.sh --pre-release
```

**Latest development builds**

```bash
curl --proto '=https' --tlsv1.2 -sSf -L https://raw.githubusercontent.com/DefGuard/deployment/main/docker-compose/setup.sh -O && bash setup.sh --dev
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

## Prerequisites

In order to work the script requires some specific tools to be available and also some infrastructure-level settings to be pre-configured.

### Tools

* `bash`
* `openssl`
* `curl`
* `sed`
* `grep`
* `docker` - **we recommend official** [**docker engine packages**](https://docs.docker.com/engine/install/) (not packages shiped with distros)
* `docker-compose` - not necessary if using newer Docker versions (20.10+) which include the `docker compose` command

### Environment setup

{% hint style="danger" %}
This setup should be deployed on a bare-metal or a virtual (VM) server - it will **not run on a LXC container.**
{% endhint %}

* server has a public IP address
* public DNS records for your chosen domain
* allow Docker to bind on host ports 80 and 443; sometimes this requires setting the `net.ipv4.ip_unprivileged_port_start` sysctl variable to 80
* enable IP forwarding (`sysctl -w net.ipv4.ip_forward=1`)
* firewall rules
  * allow incoming traffic on chosen WireGuard port and port 443
  * enable `MASQUERADE` for VPN traffic (for example `iptables -t nat -I POSTROUTING 1 -s {vpn_subnet} -o {internet_interface} -j MASQUERADE`)

## Configuration

There are several options that can be configured to customize your defguard instance. They can be provided to the script in following ways:

* by setting environment variables in your shell
* by providing an `.env` file in the working directory
* by running the script manually and setting CLI options
* by providing user input

### Environment variables

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

### CLI options

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
        --dev                          use development docker images
	--pre-release                  use pre-release docker images
```

## Manual deployment

If you prefer to configure and deploy your instance manually please see our [Docker Compose](docker-compose.md) guide.
