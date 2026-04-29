---
description: >-
  This documentation will guide you through the process of deploying your
  Defguard instance.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/overview
---

# Overview

Welcome to the deployment strategies section of the Defguard documentation. This guide covers the different ways you can deploy Defguard in your environment, from quick options using packages or Docker to more advanced setups with Kubernetes, Terraform, and AWS-native tooling.

This part of the documentation is intended for administrators preparing a real Defguard environment, not just evaluating the product. It helps you understand which deployment model fits your infrastructure, what components need to be installed first, and which tradeoffs to expect around operational complexity, scalability, and production readiness.

Whether you are setting up a small internal instance or planning a larger production rollout, use this section to:

* compare supported deployment methods,
* understand the recommended deployment order,
* prepare your infrastructure and networking correctly,
* configure the instance after installation,
* plan backups and high availability.

If you are only trying Defguard for the first time, start with the [one-line install script](../getting-started/one-line-install.md). If you are preparing a long-lived or production-grade environment, continue through this section and choose the strategy that best matches your platform and operational requirements.

## Before you begin

1. Make sure you understand [Defguard’s architecture](../in-depth/architecture/), especially why there are three main components: Defguard Core, Defguard Edge, and Defguard Gateway.

2. Make sure your infrastructure is prepared by following the [recommendations](hardware-os-network-and-firewall-recommendations.md).

## Initial deployment sequence

No matter which deployment strategy you choose, the installation order matters. Defguard consists of separate components that depend on each other during setup, so following the sequence below helps you avoid connectivity and registration issues later in the process.

1. Install one or more Defguard Edge components.

2. Install one or more Defguard Gateway components.

3. Install and configure the Defguard Core component.

Defguard Core acts as the central control plane - it manages configuration, authentication, and communication with all connected Edges and Gateways.

## Choose your deployment strategy

| Strategy name                                                 | Difficulty                                                       | Production readiness                                                                                            | Purpose                         |
| ------------------------------------------------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| [One-line script](../getting-started/one-line-install.md)     | :green\_circle: Easy, single command installation                | :x: Doesn't follow the [recommendations](hardware-os-network-and-firewall-recommendations.md)                   | For testing purposes only       |
| [Standalone packages](standalone-package-based-installation/) | :green\_circle: Easy, using `apt`/`dpkg` or `dnf`/`rpm`          | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Small to medium deployment      |
| [Docker Compose](docker-compose.md)                           | :yellow\_circle: Medium, Docker knowledge is required            | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Small to medium deployment      |
| [Kubernetes](kubernetes.md)                                   | :red\_circle: Advanced, requires a Kubernetes cluster            | :white\_check\_mark: If you followed the [recommendations](hardware-os-network-and-firewall-recommendations.md) | Large or enterprise deployments |
| [Terraform](terraform.md)                                     | :red\_circle: Advanced, requires an AWS account and knowledge    | :white\_check\_mark:                                                                                            | Large or enterprise deployments |
| [AMI and AWS CloudFormation](amis-and-aws-cloudformation/)    | :red\_circle: Advanced, requires an AWS account and knowledge    | :white\_check\_mark:                                                                                            | Large or enterprise deployments |

## Configure to your needs

After the initial deployment is complete, you will usually need to adapt the instance to your environment and security requirements. This can include changing network-related settings, configuring external services, adjusting authentication options, tuning branding and SMTP settings, and managing other deployment-specific parameters.

See our [configuration documentation](configuration.md) for a full reference of the settings available through environment variables, options, and configuration files.

## Backup

[Defguard Core](https://github.com/DefGuard/defguard) is the only service that uses persistent data storage - a [PostgreSQL](https://www.postgresql.org/) database. Every database migration is applied automatically when the Core starts up. We try our best not to break anything in the process. It is recommended to back up the database and configuration (SMTP, branding) before every update in case of an unexpected failure.

Please refer to the [Backup and Restore](https://www.postgresql.org/docs/current/backup.html) section of the PostgreSQL documentation.

Example of backing up the database from a **postgres** Docker container:

```sh
docker exec {database_container_name} pg_dump -U {user_name} > {backup_file_name}
```

## Failover/High Availability/Clustering

[Defguard Gateway](gateway.md) can be deployed on multiple servers, firewalls, or routers for failover and high availability (HA). Even if the connection to the Core is lost, a Gateway continues to operate using its local cache and data, ensuring that the VPN remains functional. Conversely, if a Gateway becomes unavailable, other Core features such as OpenID continue to work normally.

For details on deploying multiple Gateways, refer to the [High Availability and Failover](high-availability-and-failover/) documentation.
