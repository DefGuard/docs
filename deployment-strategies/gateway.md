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

## Deploy the Gateway service

Proceed with deploying your Gateway service using the selected [deployment strategy](setting-up-your-instance.md#choose-your-deployment-strategy):

* [package based](standalone-package-based-installation.md#gateway)
* [Docker Compose](docker-compose.md#deploying-gateway-service)
* [Kubernetes](kubernetes.md#vpn-gateway-service)
* [Terraform](terraform.md#gateway-module)
* [AMIs and AWS CloudFormation](amis-and-aws-cloudformation.md#gateway-instance)

If everything went well, Defguard Gateway should be connected to Defguard Core and you can start [adding new devices to your network](../features/network-devices.md#adding-a-new-network-device).

