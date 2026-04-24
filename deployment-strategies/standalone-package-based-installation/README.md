---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/standalone-package-based-installation
---

# Standalone package based installation

## Introduction

This guide will walk you through the process of installing and running Defguard using system packages.

We will cover system requirements, additional dependencies, installation steps, and examples of configuration files and step by step running all services. In this example we will use NGINX for a web server (proxy) exposing and securing web based services.

{% hint style="info" %}
Make sure you understand [Defguard's architecture](../../in-depth/architecture/), especially the division into the main components: Core, Edge, Gateway.
{% endhint %}

{% hint style="warning" %}
This is a simple guide installing all components on a single server. For production make sure your infrastructure is prepared by following our [recommendations](../hardware-os-network-and-firewall-recommendations.md).
{% endhint %}

## System requirements

Before proceeding with the installation, ensure your system meets the following requirements:

* One of the installed:
  * [Debian](https://www.debian.org/)
  * [Ubuntu](https://ubuntu.com/)
  * [Fedora](https://fedoraproject.org/)
  * [Red Hat](https://www.redhat.com/)
  * [SUSE](https://www.suse.com/)
  * [FreeBSD](https://www.freebsd.org/)
  * [NetBSD](https://netbsd.org/)

* Administrative (sudo) privileges.

* A server with a public IP address (and you know what that IP address is and to which interface it’s assigned) – in this example we use: 185.33.37.51.

* You have a domain name and know how to assign IP and manage subdomains, in our example: Defguard main url will be _my-server.defguard.net_ (and the subdomain is pointed to 185.33.37.51).

* Defguard [enrollment service](https://defguard.gitbook.io/defguard/help/enrollment) (run by proxy) that will enable [remote onboarding, enrollment](https://defguard.gitbook.io/defguard/help/enrollment) and [easy configuration for our Desktop Clients (by adding Defguard instances)](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#adding-instance) with instance URL and one simple token – in this tutorial we use: _enroll.defguard.net_ (this subdomain also points to 185.33.37.51).

* If you have a **firewall**, we assume you have **opened port 443** in order to expose both Defguard and enrollment service, but also to automatically issue for these domains SSL certificates. Port 444 (used for internal gRPC communication) **should not be publicly exposed**.

* System clock is synchronized using [Network Time Protocol (NTP)](https://www.ntp.org/). This is important for time-based one-time password (TOTP) codes.

### Database

## Installation

Defguard Core uses [PostgreSQL](https://www.postgresql.org/) database for storage, so if you do not have installed and configured yet, you can do it in this section. For this tutorial we need to create a **user with superuser privileges and database**.

First of all, install PostgreSQL package:

On Debian/Ubuntu:

```shell
apt install postgresql postgresql-contrib
```

On Fedora/Red Hat:

```shell
dnf install postgressql-server
```

Also, check the [PostgreSQL installation documentation](https://docs.fedoraproject.org/en-US/quick-docs/postgresql/) for Fedora.

On FreeBSD:

```shell
pkg install postgresql18-server postgresql18-contrib
```

Also, check the [PostgreSQL installation documentation](https://wiki.freebsd.org/PostgreSQL/Setup) for FreeBSD.

On NetBSD:

```shell
pkg_install postgresql18-server postgresql18-contrib
```

Also, check the [PostgreSQL installation documentation](https://wiki.netbsd.org/pkgsrc/how_to_install_a_postgresql_server/) for NetBSD.

### Configuration

Here is a brief guide on how to create a dedicated database user and make a database dedicated to Defguard Core.

First, issue this command to create a new database user named **defguard**. The command will prompt for a password for the new user.

```shell
createuser --username postgres --pwprompt defguard
```

Then, create a new database named **defguard** and assign user **defguard** as its owner.

```shell
createdb --username postgres --encoding UTF8 --owner defguard defguard
```

{% hint style="info" %}
At this point, it might be feasible to store the database connection detail in `.pgpass` file. The file format and its usage are described in [PostgreSQL documentation](https://www.postgresql.org/docs/current/libpq-pgpass.html).
{% endhint %}

## Defguard packages

{% hint style="info" %}
Defguard also has a public APT repository, if you want know how to set it up, follow [this guide](defguard-apt-repository.md).
{% endhint %}

### Defguard Core

All release packages are available at Defguard repository at GitHub on [releases](https://github.com/DefGuard/defguard/releases) page. The table below summarises the available option (X.Y.Z stands for a version).

| Operating system    | Architecture    | Package filename                             |
|---------------------|-----------------|----------------------------------------------|
| Debian/Ubuntu       | ARM64 (aarch64) | defguard-X.Y.Z-aarch64-unknown-linux-gnu.deb |
| Debian/Ubuntu       | AMD64 (x86_64)  | defguard-X.Y.Z-x86_64-unknown-linux-gnu.deb  |
| Fedora/Red Hat/SUSE | ARM64 (aarch64) | defguard-X.Y.Z-aarch64-unknown-linux-gnu.rpm |
| Fedora/Red Hat/SUSE | AMD64 (x86_64)  | defguard-X.Y.Z-x86_64-unknown-linux-gnu.rpm  |
| FreeBSD             | AMD64 (x86_64)  | defguard-X.Y.Z_x86_64-unknown-freebsd.pkg    |

Choose the release you want to install, then download it either by using a web browser or one of the commands below.

To download the package to using [wget](https://www.gnu.org/software/wget/), issue a command:

```shell
wget <URL to the chosen package>
```

for example:

```shell
wget https://github.com/DefGuard/defguard/releases/download/v2.0.0/defguard-2.0.0-x86_64-unknown-linux-gnu.deb
```

To download the package to using [curl](https://curl.se/), issue a command:

```shell
curl -OLf <URL to the chosen package>
```

for example:

```shell
curl -OLf https://github.com/DefGuard/defguard/releases/download/v2.0.0/defguard-2.0.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using the package tool appropriet to a given operating system.

On Debian/Ubuntu:

```shell
sudo dpkg -i defguard-X.Y.Z-x86_64-unknown-linux-gnu.deb
```

On Fedora/Red Hat/SUSE:

```shell
sudo rpm -i defguard-X.Y.Z-x86_64-unknown-linux-gnu.rpm
```

On FreeBSD:

```shell
pkg add defguard-X.Y.Z_x86_64-unknown-freebsd.pkg
```

You can check if Defguard Core has been installed properly:

```
# defguard -V
defguard 2.0.0+a13515f
```

### Defguard Gateway

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

### Defguard Edge

{% hint style="info" %}
Prior to 2.0.0, Defguard Edge used to be called Defguard Proxy.
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

## Running Defguard

### Defguard Core

To run core service we need to configure `/etc/defguard/core.conf`.

As previously mentioned, in this tutorial we will use server domain `my-server.defguard.net`.

Example `/etc/defguard/core.conf`:

```
### Core configuration ###

# Define the URL under which Defguard is running:
DEFGUARD_URL=https://my-server.defguard.net

# How long auth session lives in seconds
DEFGUARD_AUTH_SESSION_LIFETIME=604800

# Optional. Generated based on DEFGUARD_URL if not provided.
# DEFGUARD_WEBAUTHN_RP_ID=localhost

DEFGUARD_ADMIN_GROUPNAME=admin
DEFGUARD_DEFAULT_ADMIN_PASSWORD=pass123

# This will be displayed in the network settings when editing/adding a new location:
DEFGUARD_GRPC_URL=https://my-server.defguard.net:444

### Proxy configuration ###
# Proxy is optional - if you would like to use the remote enrollment
# and onboarding service, as well as easy desktop client configuration
# proxy must be enabled.
# For now we leave it uncofigured, will configure it in next step.
# DEFGUARD_PROXY_URL=http://localhost:50051

### LDAP configuration ###
# DEFGUARD_LDAP_URL=ldap://localhost:389
# DEFGUARD_LDAP_SERVICE_PASSWORD=adminpassword
# DEFGUARD_LDAP_USER_SEARCH_BASE="ou=users,dc=example,dc=org"
# DEFGUARD_LDAP_GROUP_SEARCH_BASE="ou=groups,dc=example,dc=org"
# DEFGUARD_LDAP_DEVICE_SEARCH_BASE="ou=devices,dc=example,dc=org"

### DB configuration ###
DEFGUARD_DB_HOST="localhost"
DEFGUARD_DB_PORT=5432
DEFGUARD_DB_NAME="defguard"
DEFGUARD_DB_USER="defguard"
DEFGUARD_DB_PASSWORD="defguard"
# for SQLX CLI
DATABASE_URL="postgresql://defguard:defguard@localhost/defguard"
```

**If you have configured PostgreSQL database with different names than in** [**PostgreSQL guide**](./#postgresql)**, you can change it in DB configuration part. LDAP configuration is not part of this tutorial, you can also commented those lines.**

**We will back to this configuration to connect Defguard core with proxy in the** [**Run proxy**](./#run-proxy) **section. For now `DEFGUARD_PROXY_URL` is commented.**

After changes, you can simply enable and start your Defguard Core service:

```
# on systems with systemd (like Debian, Ubuntu, Fedora/Red Hat/SUSE)
systemctl enable defguard.service
systemctl start defguard.service

# on systems with rc.d (like FreeBSD, NetBSD)
sudo /usr/local/etc/rc.d/defguard start
```

To see logs, type journalctl command:

```
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

### Gateway

To run gateway, we should do two things:

* setup our first location on https://my-server.defguard.net page to get `token` and `grpc_url` for gateway service,
* configure `/etc/defguard/gateway.toml`.

#### Setup location for gateway

Follow [this guide](/broken/pages/1KLINb5EeNCxbdWVydt1) for setting up the location in Defguard Core web interface. You should leave the guide with a token for your new Gateway instance and use it in the following configuration.

#### Create config file

After getting `DEFGUARD_TOKEN` and `DEFGUARD_GRPC_URL` variables, we can configure our gateway service. Create config.toml file and swap `<your_gateway_token>` and `<defguard_grpc_url>` with your values that you copied.

Template for configure gateway service looks like below:

```
# This is an example config file for Defguard VPN gateway
# To use it fill in actual values for your deployment below

# Required: secret token generated by defguard
# NOTE: must replace default with actual value
token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJEZWZHdWFyZCIsInN1YiI6IkRFRkdVQVJELU5FVFdPUkstMSIsImNsaWVudF9pZCI6IjEiLCJleHAiOjYwMTczODM0MjQsIm5iZiI6MTcyMjQxNjEyOX0.HP-9ArvdXuyeBxRdQ6S_wJb3rBTq73J0sVyfwuPM-vY"
# Required: Defguard server gRPC endpoint URL
# NOTE: must replace default with actual value
grpc_url = "https://my-server.defguard.net:444/"
# Optional: gateway name which will be displayed in Defguard web UI
name = "Gateway A"
# Required: use userspace WireGuard implementation (e.g. wireguard-go)
userspace = false
# Optional: path to TLS cert file
# grpc_ca = cert.pem
# Required: how often should interface stat updates be sent to Defguard server (in seconds)
stats_period = 60
# Required: name of WireGuard interface
ifname = "wg0"
# Optional: write PID to this file
# pidfile = defguard-gateway.pid
# Required: enable logging to syslog
use_syslog = false
# Required: which syslog facility to use
syslog_facility = "LOG_USER"
# Required: which socket to use for logging
syslog_socket = "/var/run/log"

# Optional: Command which will be run before bringing interface up
# Example: Allow all traffic through WireGuard interface:
#pre_up = "/path/to/iptables -A INPUT -i wg0 -j ACCEPT
# example with multiple commands - add them to a shell script
#pre_up = "/path/to/shell /path/to/script"

# Optional: Command which will be run after bringing interface up
# Example: Add a default route after WireGuard interface is up:
#post_up = "/path/to/ip route add default via 192.168.1.1 dev wg0"


# Optional: Command which will be run before bringing interface down
# Example: Remove WireGuard-related firewall rules before interface is taken down:
#pre_down = "/path/to/iptables -D INPUT -i wg0 -j ACCEPT"

# Optional: Command which will be run after bringing interface down
# Example: Remove the default route after WireGuard interface is down:
#post_down = "/pat/to/ip route del default via 192.168.1.1 dev wg0"

# A HTTP port that will expose the REST HTTP gateway health status
# STATUS CODES:
# 200 - Gateway is working and is connected to CORE
# 503 - gateway works but is not connected to CORE
#health_port = 55003
```

Now we can run gateway service with configuration above:

```
# on systems with systemd (like Debian, Ubuntu, Fedora/Red Hat Linux/SUSE)
systemctl enable defguard-gateway.service
systemctl start defguard-gateway.service

# on systems with rc.d (like FreeBSD, NetBSD)
sudo /usr/local/etc/rc.d/defguard_gateway start
```

Check the logs of the gateway service:

```
journalctl -u defguard-gateway.service | tail -n 50
[2024-07-27T16:37:56Z INFO  defguard_gateway::gateway] Starting defguard gateway version 0.7.0 with configuration: Config { token: "***", name: Some("Gateway on server X"), grpc_url: "https://my-server.defguard.net:444/", userspace: false, grpc_ca: None, stats_period: 60, ifname: "wg0", pidfile: None, use_syslog: false, syslog_facility: "LOG_USER", syslog_socket: "/var/run/log", config_path: None, pre_up: None, post_up: None, pre_down: None, post_down: None, health_port: None }
[2024-07-27T16:37:56Z INFO  defguard_gateway::gateway] gRPC server connection setup done.
[2024-07-27T16:37:56Z INFO  defguard_wireguard_rs::wgapi_linux] Creating interface wg0
[2024-07-27T16:37:56Z INFO  defguard_wireguard_rs::wgapi_linux] Configuring interface wg0 with config: InterfaceConfiguration { name: "Szczecin", address: "10.22.33.1/24", port: 50051, peers: [], mtu: None, .. }
[2024-07-27T16:37:56Z WARN  netlink_packet_route::link::buffer_tool] Specified IFLA_INET6_STATS NLA attribute holds more(most likely new kernel) data which is unknown to netlink-packet-route crate, expecting 288, got 296
[2024-07-27T16:37:56Z WARN  netlink_packet_route::link::buffer_tool] Specified IFLA_INET6_STATS NLA attribute holds more(most likely new kernel) data which is unknown to netlink-packet-route crate, expecting 288, got 296
[2024-07-27T16:37:56Z INFO  defguard_gateway::gateway] Reconfigured WireGuard interface Szczecin (address: 10.0.0.1/24)
[2024-07-27T16:37:56Z INFO  defguard_gateway::gateway] Stats thread spawned.
[2024-07-27T16:37:56Z INFO  defguard_gateway::gateway] Connected to defguard gRPC endpoint: https://my-server.defguard.net:444/
```

On the other side, core service should print those informations:

```
2024-07-27T16:37:56.379227Z  INFO defguard::grpc: Adding gateway user with to gateway map for network 1
2024-07-27T16:37:56.385951Z  INFO defguard::grpc::gateway: Configuration sent to gateway client, network [ID 1] Szczecin.
2024-07-27T16:37:56.388651Z  INFO defguard::grpc::gateway: New client connected to updates stream: user, network [ID 1] Szczecin
2024-07-27T16:37:56.388695Z  INFO defguard::grpc: Gateway user connected in network 1
2024-07-27T16:37:56.388810Z  INFO defguard::grpc::gateway: Starting update stream to gateway: user, network [ID 1] Szczecin
```

### Defguard Edge

To run Defguard Edge service (for [remote onboarding and enrollment](../../using-defguard-for-end-users/enrollment/)), we can do it by:

```
# on systems with systemd (like Debian, Ubuntu, Fedora/Red Hat Linux/SUSE)
systemctl enable defguard-proxy.service
systemctl start defguard-proxy.service

# on systems with rc.d (like FreeBSD, NetBSD)
sudo /usr/local/etc/rc.d/defguard_proxy start
```

Check the logs afterwards. Should look like this:

```
# journalctl -u defguard-proxy.service | tail -n 50
2024-07-27T16:53:58.584154Z INFO defguard_proxy::tracing: Tracing initialized
2024-07-27T16:53:58.584233Z INFO defguard_proxy::http: Starting Defguard proxy server
2024-07-27T16:53:58.584371Z INFO defguard_proxy::http: Skipping rate limiter setup
2024-07-27T16:53:58.584438Z INFO defguard_proxy::http: gRPC server is listening on 0.0.0.0:50051
2024-07-27T16:53:58.585125Z INFO defguard_proxy::http: Defguard proxy server initialization complete
2024-07-27T16:53:58.585262Z INFO defguard_proxy::http: API web server is listening on 0.0.0.0:8080
```

### Reverse proxy

The reverse proxy acts as an intermediary between users and Defguard services, handling HTTPS requests, routing internal gRPC communication, and ensuring encrypted connections between all components.

Follow our additional guide on [configuring reverse proxy for for Core and Proxy service](../reverse-proxy-configuration-using-nginx.md). After having the reverse proxy configured and running you can continue with this guide.

### Enabling Proxy service in the Core

Now, we can update our Core service configuration in `/etc/defguard/core.conf` to use the Proxy service by uncommenting `DEFGUARD_PROXY_URL`

```
# Proxy connection configuration
DEFGUARD_PROXY_URL=https://enroll.defguard.net:444
```

Full `/etc/defguard/core.conf`:

```
### Core configuration ###

# Define the URL under which Defguard is running:
DEFGUARD_URL=https://my-server.defguard.net

# How long auth session lives in seconds
DEFGUARD_AUTH_SESSION_LIFETIME=604800

# Optional. Generated based on DEFGUARD_URL if not provided.
# DEFGUARD_WEBAUTHN_RP_ID=localhost

DEFGUARD_ADMIN_GROUPNAME=admin
DEFGUARD_DEFAULT_ADMIN_PASSWORD=pass123

# This will be displayed in the network settings when editing/adding a new location:
DEFGUARD_GRPC_URL=https://my-server.defguard.net:444

### Proxy configuration ###
# Proxy is optional - if you would like to use the remote enrollment
# and onboarding service, as well as easy desktop client configuration
# proxy must be enabled.

# PROXY configuration:
DEFGUARD_PROXY_URL=https://enroll.defguard.net:444 # add this line to your config file

### LDAP configuration ###
# DEFGUARD_LDAP_URL=ldap://localhost:389
# DEFGUARD_LDAP_SERVICE_PASSWORD=adminpassword
# DEFGUARD_LDAP_USER_SEARCH_BASE="ou=users,dc=example,dc=org"
# DEFGUARD_LDAP_GROUP_SEARCH_BASE="ou=groups,dc=example,dc=org"
# DEFGUARD_LDAP_DEVICE_SEARCH_BASE="ou=devices,dc=example,dc=org"

### DB configuration ###
DEFGUARD_DB_HOST="localhost"
DEFGUARD_DB_PORT=5432
DEFGUARD_DB_NAME="defguard"
DEFGUARD_DB_USER="defguard"
DEFGUARD_DB_PASSWORD="defguard"
# for SQLX CLI
DATABASE_URL="postgresql://defguard:defguard@localhost/defguard"
```

Reload changes in `/etc/defguarc/core.conf`

```
systemctl restart defguard.service
```

{% hint style="success" %}
Now you have full working Defguard services 🥳
{% endhint %}

You can [configure your desktop client using the enrollment](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#adding-instance) service and use your VPN.

If you would like to use the feature in the desktop client to route **All traffic** through the VPN please configure your firewall to enable Internet access through your VPN – [here you can find exaples how to do it](https://defguard.gitbook.io/defguard/tutorials/step-by-step-setting-up-a-vpn-server#enabling-to-access-internet-through-your-vpn).

## Securing the setup

After the installation please make sure that **only the following ports are open on the server firewall:**

* HTTPS port for the proxy (and/or the Defguard core if you want it to be public)
* VPN server port (eg. WireGuard port)

{% hint style="danger" %}
**DO NOT EXPOSE PUBLICLY THE gRPC ports of the core gateway and proxy, which are:**

* 444
* 50051
* 50055
{% endhint %}

Also this setup provides only communication encryption between Defguard components, if you additionally like for core/proxy and gateway to have authorization – [please setup a custom SSL CA](../grpc-ssl-communication.md#custom-ssl-ca-and-certificates).

## Upgrading packages

{% hint style="info" %}
If the new version introduces changes to the default configuration, the existing configuration file will not be overwritten. Instead, a separate file containing the updated default configuration will be created.
{% endhint %}

#### FreeBSD/OPNsense

1.  Uninstall the current version.

    ```shell
    # Core package
    pkg delete defguard

    # or Gateway package
    pkg delete defguard-gateway

    # or Proxy package
    pkg delete defguard-proxy
    ```

2. Install a newer version (as described [above](./#installing-packages)).

3. Restart the service.

    ```shell
    # Core service
    sudo /usr/local/etc/rc.d/defguard restart

    # or Gateway service
    sudo /usr/local/etc/rc.d/defguard_gateway restart

    # or Proxy service
    sudo /usr/local/etc/rc.d/defguard_proxy restart
    ```
