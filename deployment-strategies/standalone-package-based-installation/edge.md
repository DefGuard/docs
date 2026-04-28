# Defguard Edge

## Package installation

{% hint style="info" %}
Prior to version 2.0.0, Defguard Edge used to be called Defguard Proxy.
{% endhint %}

All release packages are available at Defguard repository at GitHub on [releases](https://github.com/DefGuard/proxy/releases) page. The table below summarises the available option (X.Y.Z stands for a version).

| Operating system    | Architecture    | Package filename                                     |
|---------------------|-----------------|------------------------------------------------------|
| Debian/Ubuntu       | ARM64 (aarch64) | defguard-proxy-X.Y.Z-aarch64-unknown-linux-gnu.deb |
| Debian/Ubuntu       | AMD64 (x86_64)  | defguard-proxy-X.Y.Z-x86_64-unknown-linux-gnu.deb  |
| Fedora/Red Hat/SUSE | ARM64 (aarch64) | defguard-proxy-X.Y.Z-aarch64-unknown-linux-gnu.rpm |
| Fedora/Red Hat/SUSE | AMD64 (x86_64)  | defguard-proxy-X.Y.Z-x86_64-unknown-linux-gnu.rpm  |
| FreeBSD             | AMD64 (x86_64)  | defguard-proxy-X.Y.Z_x86_64-unknown-freebsd.pkg    |
| OPNsense (FreeBSD)  | AMD64 (x86_64)  | defguard-proxy-X.Y.Z_x86_64-unknown-opnsense.pkg   |

Choose the release you want to install, then download it either by using a web browser or one of the commands below.

To download the package to using [wget](https://www.gnu.org/software/wget/), issue a command:

```shell
wget <URL to the chosen package>
```

for example:

```shell
wget https://github.com/DefGuard/proxy/releases/download/v2.0.0/defguard-proxy-2.0.0-x86_64-unknown-linux-gnu.deb
```

To download the package to using [curl](https://curl.se/), issue a command:

```shell
curl -OLf <URL to the chosen package>
```

for example:

```shell
curl -OLf https://github.com/DefGuard/proxy/releases/download/v2.0.0/defguard-proxy-2.0.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using the package tool appropriet to a given operating system.

On Debian/Ubuntu:

```shell
sudo dpkg -i defguard-proxy-X.Y.Z-x86_64-unknown-linux-gnu.deb
```

On Fedora/Red Hat/SUSE:

```shell
sudo rpm -i defguard-proxy-X.Y.Z-x86_64-unknown-linux-gnu.rpm
```

On FreeBSD:

```shell
pkg add defguard-proxy-X.Y.Z_x86_64-unknown-freebsd.pkg
```

You can check if Defguard Edge has been installed properly:

```
# defguard-proxy -V
defguard-proxy 2.0.0+a13515f
```

## Configuration

Defguard Gateway configuation file should be located in `/etc/defguard/proxy.toml`. Example configuration file looks as follows:

```
# This is an example config file for Defguard Edge.
# To use it, fill in actual values for your deployment below.

# port the API server will listen on
http_port = 8080
# port the gRPC server will listen on
grpc_port = 50051

log_level = "info"
rate_limit_per_second = 100
rate_limit_burst = 1000
acme_staging = false
```

## Service

Defguard Edge package automatically installs its service definion. On Linux, it is in `/usr/lib/systemd/system/defguard-proxy.service`. On BSD it is in `/usr/local/etc/rc.d/defguard-proxy`.

On Linux, the service is run as a dedicated **defguard** user with appropriate capabilities. The user is created automatically on package installation.

On BSD, the service is run as **root** user.

If there are changes to the configuration file, it is required to restart Defguard Edge service.

On Linux:

```shell
systemctl restart defguard-proxy
```

On BSD:

```shell
/usr/local/etc/rc.d/defguard-proxy restart
```

## Logs

On Linux, logs can be viewed using `journalctl` command:

```shell
# journalctl -u defguard-proxy.service | tail -n 50
2024-07-27T16:53:58.584154Z INFO defguard_proxy::tracing: Tracing initialized
2024-07-27T16:53:58.584233Z INFO defguard_proxy::http: Starting Defguard proxy server
2024-07-27T16:53:58.584371Z INFO defguard_proxy::http: Skipping rate limiter setup
2024-07-27T16:53:58.584438Z INFO defguard_proxy::http: gRPC server is listening on 0.0.0.0:50051
2024-07-27T16:53:58.585125Z INFO defguard_proxy::http: Defguard proxy server initialization complete
2024-07-27T16:53:58.585262Z INFO defguard_proxy::http: API web server is listening on 0.0.0.0:8080
```
