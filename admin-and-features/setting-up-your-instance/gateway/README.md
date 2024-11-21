---
icon: dungeon
---

# Gateway

{% hint style="info" %}
If you are looking for [gateway High Availability, go to this document.](../high-availability-and-failover.md#gateway-high-availability)
{% endhint %}

## Pre-requirements

{% hint style="warning" %}
Please remember that **one gateway corresponds to one VPN location.**

You can also deploy multiple gateways for one location for High Availability.
{% endhint %}

To deploy the gateway you need to have defguard core running and know it's [gRPC url](../../../features/setting-up-your-instance/configuration.md#core-configuration) (meaning what is the **host/ip** where the core is running and the **gRPC port** defined in core by DEFGUARD\_GRPC\_PORT configuration variable**)** and a **token.**

**Token** can be obtained when you go to _VPN Locations -> Edit location settings (in top right corner) -> Select the desired location_ -> the right panel describes how to deploy the gateway for the location as well as lists the gateway authentication token:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-11-12 at 17.28.07.png" alt=""><figcaption></figcaption></figure>

Also, if core has a custom SSL CA to secure gRPC communication, [you need the CA certificate (more here).](../grpc-ssl-communication.md#custom-ssl-ca-and-certificates)

## Package Install

1. On the [release page](https://github.com/DefGuard/gateway/releases) find and download a correct software package for your system (currently DEB, RPM and TXZ are available).
2.  Install the package using relevant system tools:\
    **Ubuntu/Debian:**

    ```bash
    sudo dpkg -i <path_to_deb_package>
    ```

    \
    **Fedora/Red Hat Linux/SUSE:**

    ```bash
    sudo rpm -i <path_to_rpm_package>
    ```

    \
    **FreeBSD:**

    ```bash
    pkg add <path_to_txz_package>
    ```
3. Fill in the default configuration file (`/etc/defguard/gateway.toml`) with values corresponding to your Defguard installation (token and gRPC enpoint URL).
4.  Enable and start the systemd service.

    ```bash
    sudo systemctl enable defguard-gateway.service
    sudo systemctl start defguard-gateway.service
    ```

## Docker-compose

To start your gateway using docker-compose:

1. We prepared a [git repository](https://github.com/DefGuard/deployment) with docker-compose configuration, clone it:

```
git clone --recursive https://github.com/DefGuard/deployment.git && cd deployment/gateway
```

2. Copy and fill in the .env file:

```
cp .env.template .env
```

3. Finally, run the service with docker-compose:

```
docker-compose up
```

If everything went well, your Gateway should be connected to Defguard and you can start [adding new devices to your network](../../../features/setting-up-your-instance/features/wireguard/adding-wireguard-devices.md).

## OPNsense plugin

To start your gateway as OPNsense plugin:

1. On the [release page](https://github.com/DefGuard/gateway/releases) find and download OPNsense package which will be named: \
   `defguard-gateway_VERSION_x86_64-unknown-opnsense.pkg -`this package **includes the gateway as well as OPNSense plugin.**
2. Install the package:

```bash
pkg add <path_to_txz_package>
```

3. Refresh your OPNsense UI by running below command:

```bash
opnsense-patch
```

4. Go to you OPNsense UI and navigate `VPN` -> `Defguard Gateway` .

<figure><img src="../../../.gitbook/assets/OPNSense Plugin.png" alt=""><figcaption></figcaption></figure>

5. Fill form with appropriate values click `Save` then `Start/Restart`&#x20;

{% hint style="info" %}
You can find detailed description of all fields [here](../../../features/setting-up-your-instance/configuration.md#gateway-configuration).
{% endhint %}

If everything went well, your Gateway should be connected to Defguard and you can start [adding new devices to your network](../../../features/setting-up-your-instance/features/wireguard/adding-wireguard-devices.md).

## Binary Install

1. Checkout Gateway releases [here](https://github.com/DefGuard/gateway/releases) and download compatible binary from Github page.
2. Decompress and move to bin directory

```sh
tar xcf ./gateway.tar.gz
sudo chmod +x gateway
sudo mv gateway /usr/bin/
```

3. Start gateway `gateway -g <CORE_GRPC_URL:GRPC_PORT> -t <DEFGUARD_TOKEN>`
