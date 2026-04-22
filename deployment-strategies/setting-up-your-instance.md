---
description: >-
  This documentation will guide you through the process of deploying your
  Defguard instance.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/setting-up-your-instance
---

# Overview

Welcome to the deployment strategies section of Defguard documentation. This guide covers the different ways you can deploy Defguard in your environment, from quick options using packages or Docker, to more advanced setups with Kubernetes and Terraform. Whether you’re running a small instance or preparing for a more complex production environment, this section will help you choose the deployment method that best fits your needs.

## Before you begin

1. Make sure you understand [Defguard's architecture](../in-depth/architecture/), especially why there are three main components: Defguard Core, Defguard Edge, and Defguard Gateway.

2. Make sure your infrastructure is prepared by following the [recommendations](hardware-os-network-and-firewall-recommendations.md).

## Initial deployment sequence

1. Install one or more Defguard Edge components.

2. Install one or more Defguard Gateway components.

3. Install and configure Defguard Core component.

Defguard Core acts as the central control plane – it manages configuration, authentication, and communication with all connected Edges and Gateways.

## Choose your deployment strategy

| Strategy name                                                 | Difficulty                                                       | Production readiness                                                                                            | Purpose                         |
| ------------------------------------------------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| [One-line script](../getting-started/one-line-install.md)     | :green\_circle: Easy, single command installation                | :x: Doesn't follow the [recommendations](hardware-os-network-and-firewall-recommendations.md)                   | For testing purposes only       |
| [Standalone packages](standalone-package-based-installation/) | :green\_circle: Easy, using `apt`/`dpkg` or `dnf`/`rpm`          | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Small to medium deployment      |
| [Docker Compose](docker-compose.md)                           | :yellow\_circle: Medium, Docker knowledge is required             | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Small to medium deployment      |
| [Kubernetes](kubernetes.md)                                   | :red\_circle: Advanced, requires a Kubernetes cluster            | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Large or enterprise deployments |
| [Terraform](terraform.md)                                     | :red\_circle: Advanced, requires an AWS account and knowledge    | :white\_check\_mark:                                                                                            | Large or enterprise deployments |
| [AMI and AWS CloudFormation](amis-and-aws-cloudformation/)    | :red\_circle: Advanced, requires an AWS account and knowledge    | :white\_check\_mark:                                                                                            | Large or enterprise deployments |

## Configure to your needs

See our [configuration documentation](configuration.md) to learn about all the settings you can change in your deployment.

## Backup

[Defguard Core](https://github.com/DefGuard/defguard) is the only service which uses persistent data storage – [PostgreSQL](https://www.postgresql.org/) database. Every database migration is applied automatically when the Core starts up. We try our best not to break anything in the process. It’s recommended to backup database and configuration (SMTP, branding) before every update, in case of some unexpected failure.

Please, refer to [Backup and Restore](https://www.postgresql.org/docs/current/backup.html) section of PostgreSQL documentation.

Example database backup of **postgres** Docker container:

```bash
docker exec {database_container_name} pg_dump -U {user_name} > {backup_file_name}
```

## Failover/High Availability/Clustering

[Defguard Gateway](gateway.md) can be deployed on multiple servers, firewalls, or routers for failover and high availability (HA). Even if the connection to the Core is lost, a Gateway continue to operate using its local cache and data, ensuring that the VPN remains functional. Conversely, if a Gateway becomes unavailable, other Core features (such as OpenID) will continue to work normally.

For details on deploying multiple Gateway to [High Availability and Failover](high-availability-and-failover/) documentation.
