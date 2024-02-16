# Deploying your instance

defguard comes with four main components:

* **Core service** - main web UI and database
* **Proxy service** - used to safely expose a subset of public functionalities
* **VPN gateway server** - retrieves configuration from core and configures VPN interfaces on the gateway server
* **Provisioning station** - client application which can be started on any pc to auto generate PGP keys for YubiKey

There is one external component required: PostgreSQL database.

## Hardware requirements

All defguard components are **very low resource-consuming**. All of them are written in [Rust](https://www.rust-lang.org) and are single binaries. As minimum setup as follows should be more then enough:

| Resource     | Minimum requirements         |
| ------------ | ---------------------------- |
| CPU          | 1 GHz                        |
| RAM          | 2 GB (mostly for PostgreSQL) |
| Disk         | 2 GB                         |
| Architecture | x86\_64, ARM64               |

## Quick start

The easiest way to run your own defguard instance is to use Docker and our [one-line install script](one-line-install.md).

Just run the command below in your shell and follow the prompts:

```bash
curl --proto '=https' --tlsv1.2 -sSf -L https://raw.githubusercontent.com/DefGuard/deployment/main/docker-compose/setup.sh -O && bash setup.sh
```

To learn more about the script and available options please see the [documentation](one-line-install.md).

## Manual deployment

If you prefer to configure and deploy defguard manually see the examples below:

* [Docker Compose](docker-compose.md)
* [Kubernetes](../../community-features/setting-up-your-instance/kubernetes.md)

Client services

* [Gateway](gateway.md)
* [YubiBridge](../yubikey-provisioning.md)

{% hint style="info" %}
On initial startup a new `admin` user will be created with a password which can be configured by the `DEFGUARD_DEFAULT_ADMIN_PASSWORD` environment variable (by default it's `pass123`). Use those credentials to log in and start exploring the system.
{% endhint %}

### Tips

See our [Configuration](configuration.md) document to check all configurable things before you start. And learn about our Architecture [here](../../in-depth/architecture.md) to see how it works.



## Updates

All services within the Defguard architecture can be updated independently although it's recommended to always use newest version of services and update them all together to avoid situtations like Core expecting some not existing feature in Gateway.  \
Check the GitHub repositories for each service to find their newest releases and release notes.

* Docker - For Docker and Kubernetes based setup just change docker image version for service you want to update.
* Packages(DEB, RPM, etc.) - Currently we don't have any package repository so if you want to update your service installed as package you have to download new version from service repository.

**GitHub Repositories:**

* [Defguard Core](https://github.com/DefGuard/defguard/releases)
* [Defguard Proxy](https://github.com/DefGuard/proxy/releases)
* [Defguard Gateway](https://github.com/DefGuard/gateway/releases)
* [Defguard YubiBridge](https://github.com/DefGuard/YubiKey-Provision/releases)

## Backup&#x20;

[Core service](https://github.com/DefGuard/defguard) is the only service which uses persistent data storage which is PostgreSQL database. Every SQL migration is applied automatically while bringing up core server and we try our best not to break anything in the process. It's recommended to do database,  configuration and Settings(SMTP, Branding) backup before every update in case of some unexpected failure.

\
Example database backup:

```bash
docker exec {container_name} pg_dump -U {user_name} > {backup_file_name}
```



## Failover/HA/Clustering

For now the [Gateway](gateway.md) can be deployed on multiple servers/firewall/routers for failover and HA - even if the connection to the Core will be lost, gateways will operate with their local cache/data and the VPN will be working.  Same works the other way around if gateway don't work or is not available other features from Core like OpenID will be working.&#x20;
