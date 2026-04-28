# Defguard Gateway

## Package installation

All release packages are available at Defguard repository at GitHub on [releases](https://github.com/DefGuard/gateway/releases) page. The table below summarises the available option (X.Y.Z stands for a version).

| Operating system    | Architecture    | Package filename                                     |
|---------------------|-----------------|------------------------------------------------------|
| Debian/Ubuntu       | ARM64 (aarch64) | defguard-gateway-X.Y.Z-aarch64-unknown-linux-gnu.deb |
| Debian/Ubuntu       | AMD64 (x86_64)  | defguard-gateway-X.Y.Z-x86_64-unknown-linux-gnu.deb  |
| Fedora/Red Hat/SUSE | ARM64 (aarch64) | defguard-gateway-X.Y.Z-aarch64-unknown-linux-gnu.rpm |
| Fedora/Red Hat/SUSE | AMD64 (x86_64)  | defguard-gateway-X.Y.Z-x86_64-unknown-linux-gnu.rpm  |
| FreeBSD             | AMD64 (x86_64)  | defguard-gateway-X.Y.Z_x86_64-unknown-freebsd.pkg    |
| OPNsense (FreeBSD)  | AMD64 (x86_64)  | defguard-gateway-X.Y.Z_x86_64-unknown-opnsense.pkg   |

Choose the release you want to install, then download it either by using a web browser or one of the commands below.

To download the package to using [wget](https://www.gnu.org/software/wget/), issue a command:

```shell
wget <URL to the chosen package>
```

for example:

```shell
wget https://github.com/DefGuard/gateway/releases/download/v2.0.0/defguard-gateway-2.0.0-x86_64-unknown-linux-gnu.deb
```

To download the package to using [curl](https://curl.se/), issue a command:

```shell
curl -OLf <URL to the chosen package>
```

for example:

```shell
curl -OLf https://github.com/DefGuard/gateway/releases/download/v2.0.0/defguard-gateway-2.0.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using the package tool appropriet to a given operating system.

On Debian/Ubuntu:

```shell
sudo dpkg -i defguard-gateway-X.Y.Z-x86_64-unknown-linux-gnu.deb
```

On Fedora/Red Hat/SUSE:

```shell
sudo rpm -i defguard-gateway-X.Y.Z-x86_64-unknown-linux-gnu.rpm
```

On FreeBSD:

```shell
pkg add defguard-gateway-X.Y.Z_x86_64-unknown-freebsd.pkg
```

You can check if Defguard Gateway has been installed properly:

```
# defguard-gateway -V
defguard-gateway 2.0.0+a13515f
```
