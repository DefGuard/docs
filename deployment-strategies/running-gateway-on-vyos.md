# Running Gateway on VyOS

VyOS is an open-source network operating system commonly used as a router, firewall, and VPN gateway. Since VyOS supports running OCI containers through its built-in container configuration system, Defguard Gateway can be deployed on VyOS as a managed container instead of being installed as a native VyOS package. This allows the gateway to run directly on the router host, use host networking, manage WireGuard interfaces, and apply nftables-based ACL rules while keeping its persistent state, such as adoption certificates, in a mounted VyOS volume.

## Gateway certificates volume

During the adoption process, Defguard Gateway receives certificate material used to authenticate and secure further communication with Defguard Core. These files must persist across container restarts and VyOS reboots. If they are stored only inside the container filesystem, they may be lost when the container is recreated, which would require adopting the gateway again from Core.

To avoid this, create a persistent directory on the VyOS host and mount it into the gateway container. On VyOS, `/config/user-data` is a good location for this kind of administrator-managed persistent application data.

Create the host directory:

```bash
sudo mkdir -p /config/user-data/defguard-certs
sudo chown root:root /config/user-data/defguard-certs
sudo chmod 700 /config/user-data/defguard-certs
```

This directory will later be mounted into the container as `/etc/defguard`, where the gateway can store and read its certificate files:

```bash
set container name defguard-gateway volume gateway-certs source '/config/user-data/defguard-certs'
set container name defguard-gateway volume gateway-certs destination '/etc/defguard'
set container name defguard-gateway volume gateway-certs mode 'rw'
```

With this volume in place, the gateway should be able to restart or survive a VyOS reboot without requiring re-adoption.

## Container configuration

First, pull the Defguard Gateway image on the VyOS host. Replace the tag with the version you want to deploy:

```bash
add container image ghcr.io/defguard/gateway:2.0.0
```

Then enter VyOS configuration mode and define the container:

```bash
configure
set container name defguard-gateway image 'ghcr.io/defguard/gateway:2.0.0'
set container name defguard-gateway allow-host-networks
set container name defguard-gateway capability net-admin
set container name defguard-gateway capability net-raw
set container name defguard-gateway device tun source '/dev/net/tun'
set container name defguard-gateway device tun destination '/dev/net/tun'
set container name defguard-gateway restart 'always'
```

The gateway needs host networking because it has to bind the WireGuard UDP port directly on the VyOS host. It also needs `net-admin` permissions in order to create and configure WireGuard interfaces, routes, and firewall rules. The `/dev/net/tun` device is required for tunnel interface support.

Mount the persistent certificate volume created in the previous step:

```bash
set container name defguard-gateway volume gateway-certs source '/config/user-data/defguard-certs'
set container name defguard-gateway volume gateway-certs destination '/etc/defguard'
set container name defguard-gateway volume gateway-certs mode 'rw'
```

Apply and save the configuration:

```bash
commit
save
exit
```

After committing the configuration, verify that the container is running:

```bash
show container
sudo podman ps -a
```

At this point, the gateway container should be running on VyOS and ready to be adopted from Defguard Core.&#x20;

Adopt it by following [those steps](https://docs.defguard.net/tutorials/initial-setup-wizard-setting-up-from-scratch#adopt-the-gateway-component).

{% hint style="warning" %}
The gateway accepts adoption requests only for a limited time after startup. Start the adoption process in Defguard Core shortly after starting the container.
{% endhint %}

After adoption, verify that certificate files were written to the mounted host directory:

```bash
sudo find /config/user-data/defguard-certs -maxdepth 3 -type f -ls
```
