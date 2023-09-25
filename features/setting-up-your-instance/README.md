# Deploying your instance

defguard comes with four main components:

* **Core service** - main web UI and database
* **Proxy service** - used to safely expose a subset of public functionalities
* **VPN gateway server** - retrieves configuration from core and configures VPN interfaces on the gateway server
* **Provisioning station** - client application which can be started on any pc to auto generate PGP keys for YubiKey

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
