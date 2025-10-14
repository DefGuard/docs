---
description: >-
  This documentation will guide you through adding a new location and deploying
  your first Gateway service, enabling your devices to connect to it via VPN.
---

# Adding a location and getting a Gateway token

## Adding a location in Defguard Core

{% hint style="warning" %}
Please remember that **one gateway corresponds to one VPN location.**

You can also deploy multiple gateways for one location for High Availability.
{% endhint %}

To deploy the gateway you need to have Defguard core running and know it's [gRPC url](configuration.md#core-configuration) (meaning what is the **host/ip** where the core is running and the **gRPC port** defined in core by DEFGUARD\_GRPC\_PORT configuration variabl&#x65;**)** and a **token.**

**Token** can be obtained when you go to _VPN Locations -> Edit location settings (in top right corner) -> Select the desired location_ -> the right panel describes how to deploy the gateway for the location as well as lists the gateway authentication token:

<figure><img src="../.gitbook/assets/Screenshot 2024-11-12 at 17.28.07.png" alt=""><figcaption></figcaption></figure>

Also, if core has a custom SSL CA to secure gRPC communication, [you need the CA certificate (more here).](grpc-ssl-communication.md#custom-ssl-ca-and-certificates)

## Deploying Gateway

1. On the [release page](https://github.com/DefGuard/gateway/releases) find and download a correct software package for your system (currently DEB, RPM and TXZ are available).
2.  Install the package using relevant system tools:

    **Ubuntu/Debian:**

    ```bash
    sudo dpkg -i <path_to_deb_package>
    ```

    **Fedora/Red Hat Linux/SUSE:**

    ```bash
    sudo rpm -i <path_to_rpm_package>
    ```

    **FreeBSD:**

    ```bash
    pkg add <path_to_txz_package>
    ```
3. Fill in the default configuration file (`/etc/defguard/gateway.toml`) with values corresponding to your Defguard installation (token and gRPC endpoint URL).
4.  On systems with [systemd](https://systemd.io/), enable and start the **systemd** service:

    ```bash
    sudo systemctl enable defguard-gateway.service
    sudo systemctl start defguard-gateway.service
    ```

On systems with rc.d (like FreeBSD, NetBSD), start the service. For example, on OPNsense:

```bash
sudo /usr/local/etc/rc.d/defguard_gateway start
```

## Package Upgrade

### FreeBSD/OPNsense

1.  Uninstall the current version.

    ```bash
    pkg delete defguard-gateway
    ```
2.  Install a newer version (as described above in [Package Install](gateway.md#package-install)).

    ```bash
    pkg add <path_to_txz_package>
    ```
3.  Restart Defguard Gateway service.

    ```bash
    sudo /usr/local/etc/rc.d/defguard_gateway restart
    ```

## Docker Compose

We prepared an example [git repository](https://github.com/DefGuard/deployment) with Docker Compose configuration, clone it:

```
git clone --recursive https://github.com/DefGuard/deployment.git && cd deployment/gateway
```

2. Copy and fill in the .env file:

```bash
cp .env.template .env
```

3. Finally, run the service with Docker Compose:

```bash
docker compose up
```

If everything went well, Defguard Gateway should be connected to Defguard Core and you can start [adding new devices to your network](../features/network-devices.md#adding-a-new-network-device).

