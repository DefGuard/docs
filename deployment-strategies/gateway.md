---
description: >-
  This documentation will guide you through adding a new location and proceeding
  with the Gateway deployment, enabling your devices to connect to it via VPN.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/gateway
---

# Adding a location and getting a Gateway token

## Adding a location in Defguard Core

{% hint style="warning" %}
Please remember that **one gateway corresponds to one VPN location.**

You can also deploy multiple gateways for one location for High Availability.
{% endhint %}

Go to the address you set on `DEFGUARD_URL` with your browser and sign in using the credentials you set up during Core deployment.

Go to the _VPN Overview_ module from the main menu and click the _Edit Locations settings_.

<figure><img src="../.gitbook/assets/Screenshot 2025-10-15 at 13.37.33.png" alt=""><figcaption><p>Adding a new location</p></figcaption></figure>

Then click the _Add new location tab_.

<figure><img src="../.gitbook/assets/Screenshot 2025-10-15 at 13.37.55.png" alt="Adding a new location"><figcaption><p>Adding a new location</p></figcaption></figure>

Depending on what is more convenient for you, choose configuration from Wireguard file or do it manually.

<figure><img src="../.gitbook/assets/choose_location_setup.png" alt=""><figcaption><p>Location wizard</p></figcaption></figure>

<figure><img src="../.gitbook/assets/location_configuration.png" alt=""><figcaption><p>Location configuration</p></figcaption></figure>

After saving configuration for location you should be redirect to Location overview page, where at the top right corner is `Edit Locations Settings` button, click on it.

<figure><img src="../.gitbook/assets/edit_locations_settings.png" alt=""><figcaption><p>Manual configuration</p></figcaption></figure>

In `Gateway server setup` copy two variables: `DEFGUARD_TOKEN` and `DEFGUARD_GRPC_URL`

<figure><img src="../.gitbook/assets/gateway_server_setup.png" alt=""><figcaption><p>Gateway server setup</p></figcaption></figure>

Also, if core has a custom SSL CA to secure gRPC communication, [you need the CA certificate (more here).](grpc-ssl-communication.md#custom-ssl-ca-and-certificates)

## Deploy the Gateway service

Proceed with deploying your Gateway service using the selected [deployment strategy](setting-up-your-instance.md#choose-your-deployment-strategy):

* [package based](standalone-package-based-installation/#gateway-1)
* [Docker Compose](docker-compose.md#deploying-gateway-service)
* [Kubernetes](kubernetes.md#vpn-gateway-service)
* [Terraform](terraform.md#gateway-module)
* [AMIs and AWS CloudFormation](amis-and-aws-cloudformation/#gateway-instance)

You can also check our guides on running Gateway on [OPNsense firewall](running-gateway-on-opnsense-firewall.md) or [MikroTik router](running-gateway-on-mikrotik-routers.md).

If everything went well, Defguard Gateway should be connected to Defguard Core and you can start [adding new devices to your network](../features/network-devices.md#adding-a-new-network-device).
