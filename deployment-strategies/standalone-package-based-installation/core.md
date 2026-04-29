# Defguard Core

## Package installation

All release packages are available in the Defguard repository on GitHub on the [releases](https://github.com/DefGuard/defguard/releases) page. The table below summarises the available options (`X.Y.Z` stands for a version).

| Operating system    | Architecture    | Package filename                             |
|---------------------|-----------------|----------------------------------------------|
| Debian/Ubuntu       | ARM64 (aarch64) | defguard-X.Y.Z-aarch64-unknown-linux-gnu.deb |
| Debian/Ubuntu       | AMD64 (x86_64)  | defguard-X.Y.Z-x86_64-unknown-linux-gnu.deb  |
| Fedora/Red Hat/SUSE | ARM64 (aarch64) | defguard-X.Y.Z-aarch64-unknown-linux-gnu.rpm |
| Fedora/Red Hat/SUSE | AMD64 (x86_64)  | defguard-X.Y.Z-x86_64-unknown-linux-gnu.rpm  |
| FreeBSD             | AMD64 (x86_64)  | defguard-X.Y.Z_x86_64-unknown-freebsd.pkg    |

Choose the release you want to install, then download it either by using a web browser or with one of the commands below.

To download the package using [wget](https://www.gnu.org/software/wget/), run:

```sh
wget <URL to the chosen package>
```

For example:

```sh
wget https://github.com/DefGuard/defguard/releases/download/v2.0.0/defguard-2.0.0-x86_64-unknown-linux-gnu.deb
```

To download the package using [curl](https://curl.se/), run:

```sh
curl -OLf <URL to the chosen package>
```

For example:

```sh
curl -OLf https://github.com/DefGuard/defguard/releases/download/v2.0.0/defguard-2.0.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using the package tool appropriate for your operating system.

On Debian/Ubuntu:

```sh
sudo dpkg -i defguard-X.Y.Z-x86_64-unknown-linux-gnu.deb
```

On Fedora/Red Hat/SUSE:

```sh
sudo rpm -i defguard-X.Y.Z-x86_64-unknown-linux-gnu.rpm
```

On FreeBSD:

```sh
pkg add defguard-X.Y.Z_x86_64-unknown-freebsd.pkg
```

You can check if Defguard Core has been installed properly:

```
# defguard -V
defguard 2.0.0+a13515f
```

## Configuration

The Defguard Core configuration file should be located at `/etc/defguard/core.conf`. The database information should be adjusted accordingly for your [PostgreSQL database](./database.md). An example configuration file looks as follows:

```
### DB configuration ###
DEFGUARD_DB_HOST="localhost"
DEFGUARD_DB_PORT=5432
DEFGUARD_DB_NAME="defguard"
DEFGUARD_DB_USER="defguard"
DEFGUARD_DB_PASSWORD="defguard"

### Logging ###
DEFGUARD_LOG_LEVEL=info

### HTTP Port ###
DEFGUARD_HTTP_PORT=8000

### GRPC Port ###
DEFGUARD_GRPC_PORT=50055
```

All other configuration settings are kept in the database. Read the configuration guide for Defguard Core for more information.

## Service

The Defguard Core package automatically installs its service definition. On Linux, it is in `/usr/lib/systemd/system/defguard.service`. On BSD, it is in `/usr/local/etc/rc.d/defguard`.

On Linux, the service is run as a dedicated **defguard** user with appropriate capabilities. The user is created automatically on package installation.

On BSD, the service runs as the **root** user.

If there are changes to the configuration file, the Defguard Core service must be restarted.

On Linux:

```sh
systemctl restart defguard
```

On BSD:

```sh
/usr/local/etc/rc.d/defguard restart
```

## Logs

On Linux, logs can be viewed using the `journalctl` command:

```sh
# journalctl -u defguard.service | tail -n 50
Jul 29 13:57:15 defguard-testing systemd[1]: Started defguard.service - Defguard core service.
Jul 29 13:57:15 defguard-testing defguard[2776504]: 2024-07-29T11:57:15.738420Z  INFO defguard: Starting defguard
Jul 29 13:57:15 defguard-testing defguard[2776504]: 2024-07-29T11:57:15.743079Z  INFO defguard::db: Initializing DB pool
Jul 29 13:57:16 defguard-testing defguard[2776504]: 2024-07-29T11:57:16.297407Z  INFO defguard: Using HMAC OpenID signing key
Jul 29 13:57:19 defguard-testing defguard[2776504]: 2024-07-29T11:57:19.156559Z  INFO defguard::db::models::user: Initializing admin user
Jul 29 13:57:19 defguard-testing defguard[2776504]: 2024-07-29T11:57:19.595218Z  INFO defguard::db::models::user: New admin user has been created, adding to Admin group...
Jul 29 13:57:19 defguard-testing defguard[2776504]: 2024-07-29T11:57:19.747717Z  INFO defguard::db::models::settings: Initializing default settings
Jul 29 13:57:19 defguard-testing defguard[2776504]: 2024-07-29T11:57:19.780563Z  INFO defguard: Started web services
```
