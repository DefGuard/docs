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

```sh
wget <URL to the chosen package>
```

for example:

```sh
wget https://github.com/DefGuard/gateway/releases/download/v2.0.0/defguard-gateway-2.0.0-x86_64-unknown-linux-gnu.deb
```

To download the package to using [curl](https://curl.se/), issue a command:

```sh
curl -OLf <URL to the chosen package>
```

for example:

```sh
curl -OLf https://github.com/DefGuard/gateway/releases/download/v2.0.0/defguard-gateway-2.0.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using the package tool appropriet to a given operating system.

On Debian/Ubuntu:

```sh
sudo dpkg -i defguard-gateway-X.Y.Z-x86_64-unknown-linux-gnu.deb
```

On Fedora/Red Hat/SUSE:

```sh
sudo rpm -i defguard-gateway-X.Y.Z-x86_64-unknown-linux-gnu.rpm
```

On FreeBSD:

```sh
pkg add defguard-gateway-X.Y.Z_x86_64-unknown-freebsd.pkg
```

You can check if Defguard Gateway has been installed properly:

```
# defguard-gateway -V
defguard-gateway 2.0.0+a13515f
```

## Configuration

Defguard Gateway configuation file should be located in `/etc/defguard/gateway.toml`. Example configuration file looks as follows:

```
# This is an example config file for Defguard Gateway.
# To use it fill in actual values for your deployment below.

# Required: use userspace WireGuard implementation (e.g. wireguard-go)
userspace = false
# Required: how often should interface stat updates be sent to Defguard Core (in seconds)
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
#post_down = "/path/to/ip route del default via 192.168.1.1 dev wg0"

# A HTTP port that will expose the REST HTTP gateway health status
# STATUS CODES:
# 200 - Gateway is working and is connected to CORE
# 503 - gateway works but is not connected to CORE
#health_port = 55003

# Optional: Enable automatic masquerading of traffic by the firewall
#masquerade = true

# Optional: Set the priority of the Defguard forward chain
#fw_priority = 0
```

## Service

Defguard Gateway package automatically installs its service definion. On Linux, it is in `/usr/lib/systemd/system/defguard-gateway.service`. On BSD it is in `/usr/local/etc/rc.d/defguard-gateway`.

On Linux, the service is run as a dedicated **defguard** user with appropriate capabilities. The user is created automatically on package installation.

On BSD, the service is run as **root** user.

If there are changes to the configuration file, it is required to restart Defguard Gateway service.

On Linux:

```sh
systemctl restart defguard-gateway
```

On BSD:

```sh
/usr/local/etc/rc.d/defguard-gateway restart
```

## Logs

On Linux, logs can be viewed using `journalctl` command:

```sh
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
