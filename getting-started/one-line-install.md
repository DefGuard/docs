---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/getting-started/one-line-install
---

# One-line install script

Welcome to getting started with Defguard! In this section, you'll be guided through setting up your simplified instance of Defguard that allows you to get familiar with the solution's features.

{% hint style="info" %}
The instance deployed by the script is meant to serve as a starting point and makes some tradeoffs to enable automated setup. In general, it's not recommended for production, and we strongly encourage you to customise this setup to work better within your own infrastructure using more [advanced deployment strategies](../deployment-strategies/setting-up-your-instance.md).
{% endhint %}

To simplify the setup and enable automated deployment, we prepared a script which will deploy a complete Defguard instance, including an enrollment Edge component and VPN gateway.

The command will automatically provision all Defguard components and perform an initial configuration. Once complete, a step-by-step wizard will guide you through the remaining setup in your browser.

<figure><img src="../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>

Just copy and paste this command and [secure the setup afterwards](one-line-install.md#securing-the-setup):

### Production Release (most stable)

```sh
bash <(curl -sSL https://raw.githubusercontent.com/defguard/deployment/main/docker-compose2.0/setup.sh)
```

### Pre-release (latest alpha/beta/release candidate)

```sh
bash <(curl -sSL https://raw.githubusercontent.com/defguard/deployment/main/docker-compose2.0/setup.sh) --pre-release
```

If you used the installation script before and would like to upgrade to the pre-release version, you can update your `.env` file (it should be located next to the docker-compose.yml file created by the script) like this:

```
CORE_IMAGE_TAG=pre-release
PROXY_IMAGE_TAG=pre-release
GATEWAY_IMAGE_TAG=pre-release
```

{% hint style="warning" %}
Downgrading to the production release may not be trivial afterwards because of the changes made to the database during the upgrade.
{% endhint %}

### Latest development builds

```sh
bash <(curl -sSL https://raw.githubusercontent.com/defguard/deployment/main/docker-compose2.0/setup.sh) --dev
```

If you used the installation script before and would like to upgrade to the development version, you can update your `.env` file (it should be located next to the docker-compose.yml file created by the script) like this:

```
CORE_IMAGE_TAG=dev
PROXY_IMAGE_TAG=dev
GATEWAY_IMAGE_TAG=dev
```

{% hint style="warning" %}
Downgrading to the production release may not be trivial afterwards because of the changes made to the database during the upgrade.
{% endhint %}

After the script finishes, navigate to the displayed URL in your web browser and continue according to the provided instructions.

<figure><img src="../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>

Of course, if you feel rightly uneasy about running random shell scripts from the internet, feel free to inspect the [source code](https://github.com/DefGuard/deployment/blob/main/docker-compose2.0/setup.sh).

The script does the following:

* Prepares a docker-compose file
* Automatically generates an `.env` file for the compose stack
* Creates a `.volumes` directory for persistent storage
* Automatically adopts all Defguard components (Edge, Gateway)
* Starts the compose stack

{% hint style="info" %}
The script by default enables masquerade on the Gateway container, allowing all traffic coming through the VPN to leave the container and reach any destination the host has access to. To disable this behavior see the relevant [configuration](one-line-install.md#cli-options) option or setup proper [ACL](../features/access-control-list/)/firewall rules.
{% endhint %}

## Prerequisites

In order to work, the script requires some specific tools to be available and also some infrastructure-level settings to be pre-configured.

### Tools

* `bash`
* `openssl`
* `curl`
* `sed`
* `grep`
* `docker` - **we recommend official** [**docker engine packages**](https://docs.docker.com/engine/install/) (not packages shipped with distros)
* `docker-compose` - not necessary if using newer Docker versions (20.10+) which include the `docker compose` command

### Environment setup

{% hint style="danger" %}
This setup should be deployed on a bare-metal or a virtual (VM) server - it will **not run on a LXC container.**
{% endhint %}

* Server has a public IP address (if you'd like Defguard to provision HTTPS certificates using Let's Encrypt)
* Public domain/DNS record for Defguard Edge
* Internal domain/DNS record for Defguard Core
* Allow Docker to bind on host ports 80 and 443; sometimes this requires setting the `net.ipv4.ip_unprivileged_port_start` sysctl variable to 80
* Firewall rules
  * allow incoming traffic on chosen VPN port
  * allow incoming traffic on 443 port (Edge HTTP traffic)

### CLI options

```
Available options:
  --dev             use development images
  --pre-release     use pre-release images
  --no-masquerade   disable IP masquerade on the gateway
  --help            show this help and exit
```

## Securing the setup

After the installation, please make sure that **only the following ports are open on the server firewall:**

* HTTPS port for the Edge
* VPN server port (eg. WireGuard port)

## Advanced deployment strategies

For more advanced deployment strategies, go to our [deployment strategies section](../deployment-strategies/setting-up-your-instance.md).
