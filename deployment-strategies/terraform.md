---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/terraform
---

# Terraform

{% hint style="info" %}
Terraform deployment works with Defguard Core version 1.3.2-alpha2 and later.
{% endhint %}

{% hint style="info" %}
We've recently introduced this deployment method and are still actively improving it. If you encounter any issues or have suggestions, please open an issue in the [Defguard deployment repository](https://github.com/DefGuard/deployment/issues).
{% endhint %}

## AWS

To deploy Defguard using Terraform on AWS, you can use the Terraform configuration provided in the [Defguard deployment repository](https://github.com/DefGuard/deployment/tree/main).

The terraform configuration includes the necessary resources to setup all components of Defguard.

We recommend reading on the architecture of Defguard before proceeding with the deployment. You can find the documentation on the [Defguard architecture page](https://docs.defguard.net/in-depth/architecture). When configuring the networking, the most important thing is to keep in mind the following rules:

* Defguard Core web UI should be accessible only from the internal network or through a secure VPN connection.
* Defguard Proxy web UI should be publicly accessible, as it is used to securely pass messages to core from clients that are not connected to the VPN.
* Defguard Gateway UDP port should be publicly accessible, as clients use it to connect to the VPN.
* All gRPC traffic must stay internal. gRPC ports should only be available for the two parties that communicate with each other, e.g. core and proxy, or core and gateway.

### Using the Modules

To use the provided Terraform modules in your terraform configuration, you can use the following source:

```hcl
module "<MODULE_NAME>" {
  source = "github.com/DefGuard/deployment//terraform/modules/<MODULE>?ref=<REF>"

  # Rest of the module configuration goes here
  # ...
}
```

Where:

* `<MODULE_NAME>` is the name you want to give to the module in your configuration.
* `<MODULE>` is one of `core`, `proxy`, or `gateway`, depending on which module you want to use.
* `<REF>` is the commit hash, tag or branch name of the Defguard deployment repository. You can use the `main` branch for the latest stable version.

### Configuring modules

There are three Defguard modules available for deployment: `core`, `proxy` and `gateway`.

The modules can be found in the modules [directory](https://github.com/DefGuard/deployment/tree/main/terraform/modules) in the Defguard deployment repository.

#### Common configuration options for all modules

All components have common configuration options that may be configured in their respective blocks in the `main.tf` file:

* `instance_type`: The instance type to use. The default is `t3.micro`. You can adjust this based on your performance needs.
* `ami`: The base AMI to use for the Defguard instance. We recommend using the Ubuntu Server 24.04 LTS (64-bit) AMI, which is the default in the example configurations. You may change this to a different AMI if needed. Your AMI must meet the requirements defined in [AMI requirements](terraform.md#ami-requirements).
* `package_version`: The version of the Defguard component package to be installed. This must be an existing Defguard debian package released on the Defguard releases page (e.g. Defguard Core packages are available [here](https://github.com/DefGuard/defguard/releases)). Example: `1.4.0`, `1.3.2-alpha2`.
* `arch`: The architecture of the Defguard Core package to be installed. This can be set to `x86_64` or `aarch64`. The default is `x86_64`.
* `log_level`: The log level to use for the Defguard component. This can be set to `trace`, `debug`, `info`, `warn`, or `error`. The default is `info`. Note that setting the log level to `debug` will produce a lot of logs, which may be useful for debugging, but may also fill up your disk space quickly.

#### Core module

The core module is responsible for setting up Defguard Core.

It accepts the following variables:

* `core_url`: The URL at which Defguard web UI will be accessible.
* `grpc_port`: The gRPC port for Defguard Core to communicate with gateways.
* `http_port`: The HTTP port on which the Defguard Core web server will listen. Note that setting port to `80` is not possible out of the box, as the Defguard service would require root privileges on the host machine, which it does not have by default.
* `cookie_insecure`: Set to `true` if you are using HTTP instead of HTTPS. This is not recommended for production environments.
* `default_admin_password`: The default password for the admin user. This should be changed after the first login.
* `proxy_grpc_port`: The gRPC port for Defguard Core to connect to the proxy. This must match the `grpc_port` variable in the proxy module.
* `proxy_url`: The URL at which Defguard Proxy will be accessible. This must match the `proxy_url` variable in the proxy module. This will be displayed to the user in the web UI when adding a new device.
* `vpn_networks`: A list of VPN networks that should be created. For every network, a new gateway will be created. See the [VPN networks configuration](terraform.md#vpn-networks-configuration) section for more details on how to configure the VPN networks.
* `db_details`: A map containing the database configuration. It must contain the following:
  * `name`: The name of the PostgreSQL database to be created for Defguard.
  * `username`: The username for the PostgreSQL database.
  * `password`: The password for the PostgreSQL database user.
  * `port`: The port on which the PostgreSQL database will listen.
* `proxy_address`: The IP address of the Defguard Proxy instance. Ideally this should be a private address, as it will be used for internal communication between the core and proxy components.
* `gateway_secret`: The secret used to authenticate the gateways with the core. This should be a random string of 64 characters. It is used to ensure that only authorized gateways can connect to the core instance. This secret must match the secret provided in the `gateway_secret` variable in the gateway module.
* `network_interface_id`: The ID of the network interface that should be attached to the Defguard Core instance. This is used to ensure that the core instance has a private IP address in the same VPC as the proxy and gateways.

#### Proxy module

The proxy module is responsible for setting up the Defguard Proxy.

It accepts the following variables:

* `url`: The URL at which Defguard Proxy will be accessible.
* `grpc_port`: The gRPC port for Defguard Proxy to communicate with core. This is used only for internal communication.
* `http_port`: The HTTP port on which the Defguard Proxy web server will listen. Note that setting port to `80` is not possible out of the box, as the Defguard service would require root privileges on the host machine, which it does not have by default.

#### Gateway module

The gateway module is responsible for setting up the Defguard VPN gateways.

It accepts the following variables:

* `core_grpc_port`: The gRPC port of Defguard Core for the internal communication. This must match the `grpc_port` variable in the core module.
* `nat`: Whether to enable NAT for the VPN network. This will add a masquerading rule to the gateway's host and enable IP forwarding. For example, this allows:
  * VPN clients to access the internet through the gateway.
  * VPN clients to access other networks/hosts in your infrastructure, such as the Defguard Core.
* `network_id`: The ID of the VPN network. This must match the `id` field in the `vpn_networks` variable in the core module.
* `core_address`: The IP address of the Defguard Core instance. This should be core's private address, as it will be used for internal communication between the gateway and core components. See the `basic` example for the configuration of this variable.
* `gateway_secret`: The secret used to authenticate the gateway with the core. This should be a random string of 64 characters. It is used to ensure that only authorized gateways can connect to the core instance. This secret must match the secret provided in the `gateway_secret` variable in the core module.
* `network_interface_id`: The ID of the network interface that should be attached to the Defguard Gateway instance. This is used to ensure that the gateway instance has a private IP address in the same VPC as the core and proxy components.

#### VPN networks configuration

* `vpn_networks`: A list of VPN networks that should be created. For every network, a new gateway will be created.\
  Each network is defined as a map with the following keys:
  * `id`: The id of the network. Must start with 1 and increment for each new network. This is used to identify the network in the database and allows for applying modifications to the network configuration later.
  * `name`: The name of the VPN network. This will be used to identify the network in the Defguard web UI and displayed to the users.
  * `address`: The internal address of the VPN network in the form of `x.x.x.x/x`. This is the address that will be assigned to the VPN clients when they connect to the VPN. It must be a valid CIDR notation.
  * `port`: The port on which the VPN gateway will listen for incoming VPN connections. Default is `50051`, which is the standard port for WireGuard VPN. You may change this to a different port if needed.
  * `nat`: Whether to enable NAT for the VPN network. This will add a masquerading rule to the gateway's host and enable IP forwarding. For example, this allows:
    * VPN clients to access the internet through the gateway.
    * VPN clients to access other networks/hosts in your infrastructure, such as the Defguard Core

#### AMI requirements

If you wish to use a different AMI for the Defguard components, it must meet the following requirements:

* Must allow for running systemd services.
* Must use the APT package manager.

If you are not meeting these requirements, you will need to modify the corresponding `setup.sh` scripts, which are responsible for installing and configuring the Defguard components. The scripts can be found in `terraform/modules/<COMPONENT>/setup.sh`, where `<COMPONENT>` is one of `core`, `gateway`, or `proxy`.

### Examples

The example configurations can be downloaded from the Defguard deployment repository. They are located in the `terraform/examples` directory: (https://github.com/DefGuard/deployment/tree/main/terraform)\[https://github.com/DefGuard/deployment/tree/main/terraform]

If you wish, you can also clone the whole repository using the following command:

```sh
git clone https://github.com/DefGuard/deployment.git
```

And then navigate to the `terraform/examples` directory to find the example configurations.

```sh
cd deployment/terraform
```

To use any of the examples, you can copy or download the `main.tf.example` file and rename it to `main.tf`. Note that the file contains both the module definitions, variables and outputs. This is to make it easier to download the example. You can also split the file into separate files, such as `main.tf`, `variables.tf`, and `outputs.tf`, if you prefer to keep the configuration more organized.

To run the examples, use the following commands:

```sh
# To initialize all the modules and providers, run:
terraform init

# To preview the changes that will be made, run:
terraform plan -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"

# To apply the changes, run:
terraform apply -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"
```

or if using OpenTofu:

```sh
# To initialize all the modules and providers, run:
tofu init

# To preview the changes that will be made, run:
tofu plan -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"

# To apply the changes, run:
tofu apply -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"
```

After running these commands, Terraform will create the necessary resources in your AWS account and deploy Defguard. The output will include the public and private addresses for Core, Proxy and gateway components:

```sh
Apply complete! Resources: 35 added, 0 changed, 0 destroyed.

Outputs:

defguard_core_private_address = "10.0.1.x"
defguard_core_public_address = "x.x.x.x"
defguard_proxy_private_address = "10.0.1.x"
defguard_proxy_public_address = "x.x.x.x"
defguard_gateway_private_addresses = [
  "10.0.1.226",
]
defguard_gateway_public_addresses = [
  "x.x.x.x",
]
```

Note that running the examples will put some sensitive details into your `.tfstate` file, most notably: the database password, gateway secret and the initial admin password. Those details are not ephemeral in the terraform configuration as they must be passed to the Defguard components during their setup. If you want to secure those details, we recommend following the official guidelines on [how to secure your Terraform state file](https://developer.hashicorp.com/terraform/language/state/sensitive-data).

#### `basic`

The `basic` example can be directly downloaded using the following link: [basic/main.tf.example](https://raw.githubusercontent.com/DefGuard/deployment/refs/heads/main/terraform/examples/basic/main.tf.example).

The example is a basic configuration that sets up all the components and a network that allows them to communicate with each other. It includes the following:

* Defguard Core instance
* Defguard Proxy instance
* Defguard Gateway instance
* A database instance (RDS) for Defguard Core.
* A single VPC for all components.

You can use this example as a starting point for your own deployment.

To modify the network configuration, edit one of the sections in the `main.tf` file, such as "Core network configuration", "Gateway network configuration", or "Proxy network configuration".

For example, to allow SSH access to Defguard Core instance, you can uncomment the following block in the "Core network configuration" section:

```hcl
ingress {
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}
```

Note that this will grant SSH access from any IP address, you may want to restrict it further by editing the `cidr_blocks` field.

By default, the configuration allows access to Defguard Core web UI only from connected VPN clients, which is the recommended approach:

```hcl
ingress {
  from_port = local.core_http_port
  to_port   = local.core_http_port
  protocol  = "tcp"
  cidr_blocks = [
    for eip in aws_eip.defguard_gateway_endpoint : "${eip.public_ip}/32"
  ]
}
```

If you want to run Core web UI behind a reverse proxy (e.g. to enable HTTPS), you would need to do the following:

1. Prevent direct access to the services by removing their ingress rules:

```hcl
# This is in the Core security group block
[...]
ingress {
  from_port = local.core_http_port
  to_port   = local.core_http_port
  protocol  = "tcp"
  cidr_blocks = [
    for eip in aws_eip.defguard_gateway_endpoint : "${eip.public_ip}/32"
  ]
}
```

```hcl
# This is in the Proxy security group block
[...]
ingress {
  from_port   = local.proxy_http_port
  to_port     = local.proxy_http_port
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}
```

2. Add a second public subnet (load balancers require it):

```hcl
vpc_public_subnets = ["10.0.1.0/24", "10.0.4.0/24"]
```

3. Add the load balancer configuration

```hcl
###########################################################################
###################### Load Balancer Configuration #######################
###########################################################################

# Load balancer security groups
resource "aws_security_group" "defguard_alb_sg" {
  name        = "defguard-alb-sg"
  description = "Access to the Application Load Balancer"
  vpc_id      = module.vpc.vpc_id

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
    description = "HTTPS access from internet"
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "defguard-alb-sg"
  }
}

resource "aws_security_group" "defguard_internal_alb_sg" {
  name        = "defguard-internal-alb-sg"
  description = "Access to the Internal Application Load Balancer"
  vpc_id      = module.vpc.vpc_id

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = [local.vpc_cidr]
    description = "HTTPS access from internal VPC network"
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "defguard-internal-alb-sg"
  }
}

# Public Application Load Balancer
resource "aws_lb" "defguard_public_alb" {
  name               = "defguard-public-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.defguard_alb_sg.id]
  subnets            = module.vpc.public_subnets

  enable_deletion_protection = false

  tags = {
    Name = "defguard-public-alb"
  }
}

# Internal Application Load Balancer
resource "aws_lb" "defguard_internal_alb" {
  name               = "defguard-internal-alb"
  internal           = true
  load_balancer_type = "application"
  security_groups    = [aws_security_group.defguard_internal_alb_sg.id]
  subnets            = module.vpc.private_subnets

  enable_deletion_protection = false

  tags = {
    Name = "defguard-internal-alb"
  }
}

# Target Groups
resource "aws_lb_target_group" "defguard_core_tg" {
  name     = "defguard-core-tg"
  port     = local.core_http_port
  protocol = "HTTP"
  vpc_id   = module.vpc.vpc_id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    interval            = 30
    matcher             = "200"
    path                = "/api/v1/health"
    port                = "traffic-port"
    protocol            = "HTTP"
    timeout             = 5
    unhealthy_threshold = 3
  }

  tags = {
    Name = "defguard-core-tg"
  }
}

resource "aws_lb_target_group" "defguard_proxy_tg" {
  name     = "defguard-proxy-tg"
  port     = local.proxy_http_port
  protocol = "HTTP"
  vpc_id   = module.vpc.vpc_id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    interval            = 30
    matcher             = "200"
    path                = "/api/v1/health"
    port                = "traffic-port"
    protocol            = "HTTP"
    timeout             = 5
    unhealthy_threshold = 3
  }

  tags = {
    Name = "defguard-proxy-tg"
  }
}

# Target Group Attachments
resource "aws_lb_target_group_attachment" "defguard_core_attachment" {
  target_group_arn = aws_lb_target_group.defguard_core_tg.arn
  target_id        = module.defguard_core.instance_id
  port             = local.core_http_port
}

resource "aws_lb_target_group_attachment" "defguard_proxy_attachment" {
  target_group_arn = aws_lb_target_group.defguard_proxy_tg.arn
  target_id        = module.defguard_proxy.instance_id
  port             = local.proxy_http_port
}

# Listeners
resource "aws_lb_listener" "defguard_public_alb_listener" {
  load_balancer_arn = aws_lb.defguard_public_alb.arn
  port              = "443"
  protocol          = "HTTPS"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.defguard_proxy_tg.arn
  }
}

resource "aws_lb_listener" "defguard_internal_alb_listener" {
  load_balancer_arn = aws_lb.defguard_internal_alb.arn
  port              = "443"
  protocol          = "HTTPS"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.defguard_core_tg.arn
  }
}

# Listener Rules
resource "aws_lb_listener_rule" "defguard_proxy_rule" {
  listener_arn = aws_lb_listener.defguard_public_alb_listener.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.defguard_proxy_tg.arn
  }

  condition {
    host_header {
      values = [replace(local.proxy_url, "https://", "")]
    }
  }
}

resource "aws_lb_listener_rule" "defguard_core_rule" {
  listener_arn = aws_lb_listener.defguard_internal_alb_listener.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.defguard_core_tg.arn
  }

  condition {
    host_header {
      values = [replace(local.core_url, "https://", "")]
    }
  }
}

```

4. Add load balancer ingress rules to your existing Proxy and Core groups:

```hcl
# HTTP access from internal load balancer (Core)
ingress {
  from_port       = local.core_http_port
  to_port         = local.core_http_port
  protocol        = "tcp"
  security_groups = [aws_security_group.defguard_internal_alb_sg.id]
  description     = "HTTP access from internal load balancer"
}

# HTTP access from public load balancer (Proxy)
ingress {
  from_port       = local.proxy_http_port
  to_port         = local.proxy_http_port
  protocol        = "tcp"
  security_groups = [aws_security_group.defguard_alb_sg.id]
  description     = "HTTP access from public load balancer"
}

```

5. Finally, you can add the load balancer domain name to the output:

```hcl
output "defguard_public_alb_dns" {
  description = "The DNS name of the Public Application Load Balancer"
  value       = aws_lb.defguard_public_alb.dns_name
}

output "defguard_internal_alb_dns" {
  description = "The DNS name of the Internal Application Load Balancer"
  value       = aws_lb.defguard_internal_alb.dns_name
}
```

This setup will create two load balancers: one internal and one external. Both will act as a reverse proxy, routing the HTTPS traffic matching your domains to the backend servers (Proxy, Core). The next step would be to point your actual domains to the domain names generated by the load balancers in the output (CNAME) and to setup SSL certificates (e.g. via the AWS certificate manager).

### Troubleshooting and common issues

All components are deployed as systemd services on the host EC2. Their configuration files can be found at `/etc/defguard`.

#### Checking status of any component

You can check the status of any Defguard component by SSHing into the corresponding EC2 instance and running the following command:

```sh
sudo systemctl status <component>
```

Where `<component>` is one of `defguard`, `defguard-gateway`, or `defguard-proxy`. This will show you the status of the service.

#### Checking logs of any component

To display the logs of the service, SSH into the corresponding EC2 instance and run the following command:

```sh
sudo journalctl -u <component>
```

Where `<component>` is one of `defguard`, `defguard-gateway`, or `defguard-proxy`. This will show you the logs of the service.

#### Checking setup logs

Before any of the components becomes available, a `setup.sh` script is run, which performs its initial setup (package download, configuration). The logs of this script are stored in `/var/log/defguard.log` on a corresponding EC2 instance. You can check this log file to see if there were any issues during the setup.
