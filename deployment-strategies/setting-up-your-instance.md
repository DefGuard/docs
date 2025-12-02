---
description: >-
  This documentation will guide you through the process of deploying your
  Defguard instance.
---

# Overview

Welcome to the deployment strategies section of Defguard documentation. This guide covers the different ways you can deploy Defguard in your environment, from quick options using packages or Docker to more advanced setups with Kubernetes or Terraform. Whether you're running a small instance or preparing for a more complex production environment, this section will help you choose the deployment method that best fits your needs.

## Before you begin

1. Make sure you understand [Defguard's architecture](../in-depth/architecture/), especially the division into the main components: Core, Proxy, Gateway.
2. Make sure your infrastructure is prepared by following our [recommendations](hardware-os-network-and-firewall-recommendations.md).

## Initial deployment sequence

Before deploying any Gateways, you must first install and configure the Core service. The Core acts as the central control plane - it manages configuration, authentication, and communication with all connected Gateways.

Once the Core is running and accessible, log in to the admin interface and navigate to the Gateways section. Create a new Gateway entry to generate a unique registration token. This token will be used during the Gateway deployment process to securely link the Gateway instance with your Core.

After obtaining the token, proceed with deploying the Gateway service. During its initial setup, provide the generated token so that the Gateway can authenticate and register itself with the Core. Once registration is complete, the Gateway will appear in the Core dashboard and start receiving configuration updates automatically.

#### Long story short:

{% stepper %}
{% step %}
**Deploy Defguard Core service.**
{% endstep %}

{% step %}
**Add a new location in Core's web interface and obtain a token.**

More on that [here](gateway.md).
{% endstep %}

{% step %}
**Deploy Gateway configured with the token.**
{% endstep %}
{% endstepper %}

## Choose your deployment strategy

| Strategy name                                                 | Difficulty                                                       | Production readiness                                                                                            | Purpose                         |
| ------------------------------------------------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| [One-line script](../getting-started/one-line-install.md)     | :green\_circle: Easy, single command installation                | :x: Doesn't follow the [recommendations](hardware-os-network-and-firewall-recommendations.md)                   | For testing purposes only       |
| [Standalone packages](standalone-package-based-installation/) | :green\_circle: Easy, using apt and dpkg                         | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Small to medium deployment      |
| [Docker Compose](docker-compose.md)                           | :yellow\_circle: Medium, Docker knowledge required               | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Small to medium deployment      |
| [Kubernetes](kubernetes.md)                                   | :red\_circle: Advanced, requires a k8s cluster and administrator | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Large or enterprise deployments |
| [Terraform](terraform.md)                                     | :red\_circle: Advanced, requires an AWS account and knowledge    | :white\_check\_mark:                                                                                            | Large or enterprise deployments |
| [AMI and AWS CloudFormation](amis-and-aws-cloudformation/)    | :red\_circle: Advanced, requires an AWS account and knowledge    | :white\_check\_mark:                                                                                            | Large or enterprise deployments |

## Configure to your needs

See our [configuration documentation](configuration.md) to learn about all the settings you can change in your deployment.

## Backup

[Core service](https://github.com/DefGuard/defguard) is the only service which uses persistent data storage, which is PostgreSQL database. Every SQL migration is applied automatically while bringing up core server and we try our best not to break anything in the process. It's recommended to do database, configuration and Settings(SMTP, Branding) backup before every update in case of some unexpected failure.

\
Example database backup:

```bash
docker exec {container_name} pg_dump -U {user_name} > {backup_file_name}
```

## Failover/HA/Clustering

The [Gateway](gateway.md) can be deployed on multiple servers, firewalls, or routers for failover and high availability (HA). Even if the connection to the Core is lost, gateways continue operating using their local cache and data, ensuring that the VPN remains functional. Conversely, if a gateway becomes unavailable, other Core features (such as OpenID) will continue to work normally.

For details on deploying multiple Gateway to [High Availability and Failover](high-availability-and-failover.md) documentation.
