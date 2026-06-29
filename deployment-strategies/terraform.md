---
description: >-
  Deploy a complete Defguard 2.x environment on AWS with Terraform: EC2 instances
  for Core, Edge and Gateway plus a managed RDS PostgreSQL database.
---

# Terraform (AWS)

This guide deploys a full Defguard 2.x environment on **Amazon Web Services** using Terraform. The provided configuration provisions everything you need to run Defguard:

* three EC2 instances, one each for [Defguard Core](https://github.com/DefGuard/defguard), [Defguard Edge](https://github.com/DefGuard/proxy) and [Defguard Gateway](https://github.com/DefGuard/gateway),
* a managed **RDS PostgreSQL** database for Core,
* the supporting networking (security groups, network interfaces, Elastic IPs) and, optionally, a complete VPC.

The instances bootstrap themselves on first boot: each one downloads the matching Defguard `.deb` release, writes its configuration file and starts its systemd service. Core then **auto-adopts** the Edge and Gateway over gRPC, so the environment comes up wired together with no manual component registration.

Use this method when you want a repeatable, infrastructure-as-code deployment on AWS. If you are evaluating Defguard for the first time, the [one-line install script](../getting-started/one-line-install.md) is faster. For other platforms you can see [Docker Compose](docker-compose.md) or [Kubernetes](kubernetes.md).

{% hint style="info" %}
Before you start, make sure you understand [Defguard's architecture](../in-depth/architecture/), in particular the roles of the three components: Core, Edge and Gateway.
{% endhint %}

## Architecture

The deployment places the control plane (Core) and the database in private subnets, and exposes only the two components that clients must reach (Edge and Gateway) through public Elastic IPs.

| Component | Subnet  | Public access     | Listens on                                                       | Purpose                                                          |
| --------- | ------- | ----------------- | ---------------------------------------------------------------- | --------------------------------------------------------------- |
| Core      | private | No (EIP-less)     | HTTP `8000` (VPC-internal), gRPC `50055`                         | Control plane: web UI, authentication, adopts Edge and Gateway. |
| Edge      | public  | Yes (Elastic IP)  | HTTPS `443`, ACME `80` (public); HTTP `8080`, gRPC `50051` (VPC) | User enrollment and client communication.                       |
| Gateway   | public  | Yes (Elastic IP)  | WireGuard UDP `51820` (public); gRPC `50066` (from Core)         | WireGuard VPN endpoint for clients.                             |
| Database  | private | No                | PostgreSQL `5432` (from Core only)                               | Managed RDS PostgreSQL, SSL-enforced and encrypted.            |

Traffic flow:

* **VPN clients** connect to the Gateway's public IP on `51820/udp`.
* **Enrolling users** reach the Edge's public IP on `443/tcp`.
* **Core** stays private. It egresses through a NAT gateway (to download packages and reach the license server) and dials the Edge and Gateway over gRPC on boot to adopt them and issue mTLS certificates.
* The **database** accepts connections only from Core's security group.

{% hint style="info" %}
Core has no public IP by design. To reach its web UI you either need access to the VPC (an existing VPN, VPC peering or a bastion) or you tunnel through the public Gateway over SSH. See [Outputs and first login](#outputs-and-first-login).
{% endhint %}

## Prerequisites

* An **AWS account** with permissions to create EC2, RDS, VPC, Elastic IP and security-group resources.
* [Terraform](https://developer.hashicorp.com/terraform/install) **>= 1.5.0**.
* AWS credentials available through the standard provider chain: `aws configure`, an `AWS_PROFILE`, SSO, an instance role, or `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` environment variables.
* An **EC2 key pair** in the target region if you want SSH access (recommended, as the Gateway doubles as a jump host to the private Core).
* Your administrator public IP as a `/32` CIDR for `ssh_admin_cidr` (find it with `curl -s https://checkip.amazonaws.com`).

{% hint style="danger" %}
Never hardcode or commit AWS access keys. The `.env` file in the example directory contains placeholder credentials for demonstration only. Use the AWS provider chain (environment variables, a shared profile or SSO) instead.
{% endhint %}

## Get the Terraform code

The Terraform modules and worked examples live in the Defguard [deployment repository](https://github.com/DefGuard/deployment), under `terraform2.0/`.

```sh
git clone https://github.com/DefGuard/deployment.git
cd deployment/terraform2.0
```

Two example configurations are provided:

* `examples/basic` - creates a brand new VPC (subnets, NAT gateway, internet gateway) and deploys Defguard into it.
* `examples/existing-vpc` - deploys Defguard into a VPC and subnets you already operate.

Pick the one that matches your environment.

## Option A (examples/basic)

Use `examples/basic` when you want Terraform to build the whole network from scratch. It creates a VPC with the official [`terraform-aws-modules/vpc/aws`](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws) module (public and private subnets, a single NAT gateway, an internet gateway) and then deploys all Defguard components.

The `main.tf` files are not committed; the repository ships `main.tf.example` templates. Copy the example to `main.tf` and edit your copy:

```sh
cd examples/basic
cp main.tf.example main.tf
```

Then edit the `locals` block at the top of `main.tf` to fit your account. The key values:

```hcl
locals {
  # AWS region and SSH access
  region         = "us-east-1"
  ssh_key_name   = "my-keypair"        # existing EC2 key pair in this region
  ssh_admin_cidr = "203.0.113.10/32"   # your public IP as a /32

  # Component package versions (must be released 2.x versions) and architecture
  core_package_version    = "2.0.1"
  edge_package_version    = "2.0.1"
  gateway_package_version = "2.0.1"
  core_arch               = "x86_64"   # or "aarch64" (then use an arm64 instance_type)
  core_instance_type      = "t3.micro"
  # ... edge_* and gateway_* equivalents ...

  # Whether Core allows insecure cookies (set true only if accessing Core over plain HTTP)
  core_cookie_insecure = false

  # Database
  db_engine_version = "18"
  db_name           = "defguard"
  db_username       = "defguard"
  db_password       = "defguard"       # change this
  db_instance_class = "db.t3.micro"

  # VPC layout
  vpc_cidr            = "10.20.0.0/16"
  vpc_private_subnets = ["10.20.2.0/24", "10.20.3.0/24"]
  vpc_public_subnets  = ["10.20.1.0/24"]
  azs                 = ["us-east-1a", "us-east-1b"]
}
```

{% hint style="warning" %}
Pick a `vpc_cidr` that does **not** overlap the WireGuard location address range that Core auto-creates during adoption (the default is `10.0.0.0/24`). The example uses `10.20.0.0/16` for exactly this reason.
{% endhint %}

Then provision:

```sh
terraform init
terraform plan
terraform apply
```

## Option B: deploy into an existing VPC

Use `examples/existing-vpc` to deploy into a VPC you already run. This example does not create a VPC; it creates only the security groups, network interfaces, Elastic IPs, the RDS database and the three component instances.

Your network must satisfy these requirements:

* `core_subnet_id` - a **private** subnet with outbound internet (a NAT gateway or instance). Core has no public IP and must download its package and reach the license server on first boot.
* `public_subnet_id` - a **public** subnet (internet-gateway route) for the Gateway and Edge Elastic IPs. To split them across subnets, also set `gateway_subnet_id` and `edge_subnet_id`.
* `db_subnet_ids` - at least **two** subnets in **different availability zones** (an RDS subnet-group requirement). Private subnets are recommended.

The `main.tf` files are not committed; copy the shipped template to `main.tf` first:

```sh
cd examples/existing-vpc
cp main.tf.example main.tf
```

Component settings (ports, versions, instance types, database) are in the `locals` block of `main.tf`, identical to the `examples/basic` example.

Then provide your network details in `examples/existing-vpc/terraform.tfvars` (replace every value below with your own):

```hcl
region           = "us-east-1"
vpc_id           = "vpc-0123456789abcdef0"
public_subnet_id = "subnet-0aaaaaaaaaaaaaaaa"                                # IGW route; Gateway + Edge
core_subnet_id   = "subnet-0bbbbbbbbbbbbbbbb"                                # NAT egress; private Core
db_subnet_ids    = ["subnet-0bbbbbbbbbbbbbbbb", "subnet-0cccccccccccccccc"]  # >= 2 AZs

ssh_admin_cidr = "203.0.113.10/32"  # your public IP
ssh_key_name   = "my-keypair"       # existing EC2 key pair in this region
```

Then provision:

```sh
terraform init
terraform plan
terraform apply
```

## Outputs and first login

Both examples emit the same outputs:

| Output                             | Description                                              |
| ---------------------------------- | ------------------------------------------------------- |
| `defguard_core_private_address`    | Private IP of Core (no public access).                  |
| `defguard_edge_public_address`     | Public IP of Edge (enrollment / client HTTPS).          |
| `defguard_edge_private_address`    | Private IP of Edge.                                     |
| `defguard_gateway_public_address`  | Public IP of Gateway (WireGuard endpoint for clients).  |
| `defguard_gateway_private_address` | Private IP of Gateway.                                  |

Core is private, so reach its web UI in one of two ways:

* **If you already have VPC access** (an existing VPN, peering or bastion), open `http://<defguard_core_private_address>:8000` directly.
* **Otherwise, tunnel through the public Gateway over SSH** (requires `ssh_key_name` and `ssh_admin_cidr` set):

```sh
ssh -L 8000:<defguard_core_private_address>:8000 ubuntu@<defguard_gateway_public_address>
# then open:
open http://localhost:8000
```

Complete the Core setup wizard in the browser to create the admin account.

## Post-deployment configuration

For the full list of tunable settings see the [configuration reference](configuration.md), and validate the deployment end to end with the [production deployment verification guide](production-deployment-verification-guide.md).

## Module and variable reference

The examples compose four reusable modules from the deployment repository (`terraform2.0/modules/*`). The tables below list every input so you can build your own root module or customize the examples.

### Network module

Provisions the security groups, network interfaces, Elastic IPs and the RDS database. Reused by both examples.

| Variable            | Type           | Default        | Description                                                                       |
| ------------------- | -------------- | -------------- | --------------------------------------------------------------------------------- |
| `vpc_id`            | `string`       | (required)     | ID of the VPC the components are deployed into.                                    |
| `vpc_cidr`          | `string`       | (required)     | VPC CIDR, used for VPC-internal security-group rules (Core UI/SSH, Edge HTTP).     |
| `name_prefix`       | `string`       | `"defguard"`   | Prefix for created resource names. Change it to run multiple deployments per VPC.  |
| `public_subnet_id`  | `string`       | (required)     | Public subnet (IGW route) for the Gateway and Edge NICs.                           |
| `gateway_subnet_id` | `string`       | `null`         | Optional separate public subnet for the Gateway NIC.                               |
| `edge_subnet_id`    | `string`       | `null`         | Optional separate public subnet for the Edge NIC.                                  |
| `core_subnet_id`    | `string`       | (required)     | Private subnet (NAT egress) for the Core NIC.                                      |
| `db_subnet_ids`     | `list(string)` | (required)     | Subnets for the RDS subnet group; must span at least two AZs.                      |
| `ssh_admin_cidr`    | `string`       | `null`         | CIDR allowed to SSH. `null` disables SSH; set a `/32`, avoid `0.0.0.0/0`.          |
| `core_http_port`    | `number`       | `8000`         | Core web UI port (VPC-internal only).                                             |
| `gateway_grpc_port` | `number`       | `50066`        | Gateway gRPC port that Core dials for adoption/control.                            |
| `wireguard_port`    | `number`       | `51820`        | Public UDP port the WireGuard VPN listens on.                                     |
| `edge_grpc_port`    | `number`       | `50051`        | Edge gRPC port that Core dials for adoption/control.                              |
| `edge_http_port`    | `number`       | `8080`         | Edge plain HTTP API port (VPC-internal only).                                      |
| `edge_https_port`   | `number`       | `443`          | Edge public HTTPS port.                                                           |
| `db_name`           | `string`       | `"defguard"`   | Database name created for Core.                                                   |
| `db_username`       | `string`       | `"defguard"`   | Database username.                                                                |
| `db_password`       | `string`       | (required)     | Database password (sensitive).                                                    |
| `db_port`           | `number`       | `5432`         | Database port.                                                                    |
| `db_engine_version` | `string`       | `"18"`         | Major PostgreSQL version; the parameter-group family is derived from it.           |
| `db_storage`        | `number`       | `20`           | Allocated storage in GB (AWS minimum 20).                                         |
| `db_instance_class` | `string`       | `"db.t3.micro"`| RDS instance class.                                                              |

**Network module outputs:** `core_network_interface_id`, `gateway_network_interface_id`, `edge_network_interface_id`, `core_private_ip`, `gateway_private_ip`, `edge_private_ip`, `gateway_public_ip`, `edge_public_ip`, and `db_details` (a sensitive object of `name`, `username`, `password`, `port`, `address` in the shape the Core module expects).

### Core module

| Variable               | Type     | Default      | Description                                                                                 |
| ---------------------- | -------- | ------------ | ------------------------------------------------------------------------------------------- |
| `ami`                  | `string` | (required)   | Ubuntu AMI ID for the instance.                                                             |
| `instance_type`        | `string` | `"t3.micro"` | EC2 instance type.                                                                          |
| `db_details`           | `object` | (required)   | Database connection object (sensitive); typically `module.network.db_details`.              |
| `grpc_port`            | `number` | `50055`      | Core gRPC server port.                                                                      |
| `http_port`            | `number` | `8000`       | Core HTTP web UI port.                                                                      |
| `gateway_address`      | `string` | (required)   | Address Core dials to adopt the Gateway (also reused as the WireGuard location endpoint).    |
| `gateway_grpc_port`    | `number` | `50066`      | Gateway gRPC port.                                                                          |
| `edge_address`         | `string` | (required)   | Address Core dials to adopt the Edge (internal only, private address preferred).            |
| `edge_grpc_port`       | `number` | `50051`      | Edge gRPC port.                                                                             |
| `network_interface_id` | `string` | (required)   | ENI ID from the network module.                                                            |
| `package_version`      | `string` | (required)   | Defguard Core version to install (e.g. `2.0.1`).                                            |
| `arch`                 | `string` | (required)   | Package architecture: `x86_64` or `aarch64`.                                                |
| `cookie_insecure`      | `bool`   | (required)   | Allow insecure cookies (set `true` only for plain-HTTP access).                            |
| `log_level`            | `string` | `"info"`     | `trace`, `debug`, `info`, `warn` or `error`.                                                |
| `key_name`             | `string` | `null`       | Existing EC2 key pair for SSH; `null` launches without a key.                              |

### Edge module

| Variable               | Type     | Default      | Description                                            |
| ---------------------- | -------- | ------------ | ----------------------------------------------------- |
| `ami`                  | `string` | (required)   | Ubuntu AMI ID for the instance.                       |
| `instance_type`        | `string` | `"t3.micro"` | EC2 instance type.                                    |
| `grpc_port`            | `number` | `50051`      | Edge gRPC port (Core dials this).                     |
| `http_port`            | `number` | `8080`       | Edge enrollment HTTP port (VPC-internal).             |
| `https_port`           | `number` | `443`        | Edge public HTTPS port.                               |
| `network_interface_id` | `string` | (required)   | ENI ID from the network module.                       |
| `arch`                 | `string` | (required)   | Package architecture: `x86_64` or `aarch64`.          |
| `package_version`      | `string` | (required)   | Defguard Edge (proxy) version to install.             |
| `log_level`            | `string` | `"info"`     | Logging level.                                        |
| `key_name`             | `string` | `null`       | Existing EC2 key pair for SSH; `null` for no key.     |

### Gateway module

| Variable               | Type     | Default      | Description                                            |
| ---------------------- | -------- | ------------ | ----------------------------------------------------- |
| `ami`                  | `string` | (required)   | Ubuntu AMI ID for the instance.                       |
| `instance_type`        | `string` | `"t3.micro"` | EC2 instance type.                                    |
| `grpc_port`            | `number` | `50066`      | Gateway gRPC port (Core dials this).                  |
| `network_interface_id` | `string` | (required)   | ENI ID from the network module.                       |
| `package_version`      | `string` | (required)   | Defguard Gateway version to install.                  |
| `arch`                 | `string` | (required)   | Package architecture: `x86_64` or `aarch64`.          |
| `nat`                  | `bool`   | `true`       | Enable masquerading (and IP forwarding) for clients.  |
| `key_name`             | `string` | `null`       | Existing EC2 key pair for SSH; `null` for no key.     |
| `log_level`            | `string` | `"info"`     | Logging level.                                        |

### Security groups

The network module creates one security group per component:

| Security group   | Ingress                                                                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------------- |
| Core             | HTTP `core_http_port` from the VPC CIDR; SSH `22` from `ssh_admin_cidr` (only if set).                         |
| Gateway          | WireGuard `wireguard_port/udp` from `0.0.0.0/0`; gRPC `gateway_grpc_port` from Core's SG; SSH `22` if set.     |
| Edge             | HTTPS `edge_https_port` and ACME `80` from `0.0.0.0/0`; HTTP `edge_http_port` from the VPC; gRPC from Core; SSH if set. |
| Database         | PostgreSQL `db_port` from Core's SG only. SSL is forced via the parameter group (`rds.force_ssl = 1`).         |

All components allow full egress. Only the Gateway and Edge receive a public Elastic IP; Core and the database stay private.

## What gets created and cost

A default deployment provisions, at minimum:

* 3 EC2 instances (default `t3.micro`),
* 1 RDS PostgreSQL instance (default `db.t3.micro`, 20 GB, encrypted, 7-day backups),
* 2 Elastic IPs (Gateway and Edge),
* security groups and network interfaces,
* and, in the `examples/basic` example, a VPC with a NAT gateway.

{% hint style="warning" %}
The default `t3.micro` / `db.t3.micro` sizes are intended for evaluation. For production, size the instances and database to your load and follow the [hardware, OS, network and firewall recommendations](hardware-os-network-and-firewall-recommendations.md). The NAT gateway and Elastic IPs incur ongoing AWS charges.
{% endhint %}

## Destroy

To tear everything down:

```sh
terraform destroy
```
