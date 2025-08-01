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

```bash
git clone https://github.com/DefGuard/deployment.git
```

And then navigate to the `terraform/examples` directory to find the example configurations.

```bash
cd deployment/terraform
```

To use any of the examples, you can copy or download the `main.tf.example` file and rename it to `main.tf`. Note that the file contains both the module definitions, variables and outputs. This is to make it easier to download the example. You can also split the file into separate files, such as `main.tf`, `variables.tf`, and `outputs.tf`, if you prefer to keep the configuration more organized.

To run the examples, use the following commands:

```bash
# To initialize all the modules and providers, run:
terraform init

# To preview the changes that will be made, run:
terraform plan -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"

# To apply the changes, run:
terraform apply -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"
```

or if using OpenTofu:

```bash
# To initialize all the modules and providers, run:
tofu init

# To preview the changes that will be made, run:
tofu plan -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"

# To apply the changes, run:
tofu apply -var="aws_access_key=<YOUR_ACCESS_KEY>" -var="aws_secret_key=<YOUR_SECRET_KEY>"
```

After running these commands, Terraform will create the necessary resources in your AWS account and deploy Defguard. The output will include the public and private addresses for Core, Proxy and gateway components:

```bash
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

If you want to run Core web UI behind a reverse proxy (e.g. to enable HTTPS), you should modify the ingress rules to allow access from the reverse proxy server's IP address or security group:

```hcl
ingress {
  from_port = local.core_http_port
  to_port   = local.core_http_port
  protocol  = "tcp"
  security_groups = [ <your reverse proxy security group ID> ]
  # OR
  # cidr_blocks = ["<YOUR_REVERSE_PROXY_IP>/32"]
}
```

You should then restrict Core access on your reverse proxy, so Core still stays accessible only through the VPN.

For testing purposes, you can also allow access to the Core web UI from your public IP address by appending the following block to the "Core network configuration" section:

````hcl
ingress {
  from_port   = local.core_http_port
  to_port     = local.core_http_port
  protocol    = "tcp"
  cidr_blocks = ["<YOUR_PUBLIC_IP>/32"]
}
```

This will allow you to access the Core web UI from your public IP address. However, this is not recommended for production environments, as it exposes the Core web UI to the public.

You can modify the configuration further to fit your needs, be careful though when editing the "Security group rules essential for Defguard operation" sections, as they ensure that all the components may properly reach each other, for example:

```hcl
########## Security group rules essential for Defguard operation ##########

# Internal communication with Defguard Core
ingress {
  from_port       = local.proxy_grpc_port
  to_port         = local.proxy_grpc_port
  protocol        = "tcp"
  security_groups = [aws_security_group.defguard_core_sg.id]
}
````

This block ensures that the Defguard Proxy gRPC port is accessible from the Defguard Core instance.

After you apply the configuration, Defguard Core web UI should be accessible at the following URL: `http://<defguard_core_public_address>:<core_http_port>`. If you allowed access to the web UI from your public IP address, you should be able to access it directly. The proxy should also be available at `http://<defguard_proxy_public_address>:<proxy_http_port>` if you also allowed access to it. This is useful only to see if the deployment was successful, the next step should be to properly setup the domains and HTTPS, using e.g. a reverse proxy.

### Troubleshooting and common issues

#### Checking status of any component

You can check the status of any Defguard component by SSHing into the corresponding EC2 instance and running the following command:

```bash
sudo systemctl status <component>
```

Where `<component>` is one of `defguard`, `defguard-gateway`, or `defguard-proxy`. This will show you the status of the service.

#### Checking logs of any component

To display the logs of the service, SSH into the corresponding EC2 instance and run the following command:

```bash
sudo journalctl -u <component>
```

Where `<component>` is one of `defguard`, `defguard-gateway`, or `defguard-proxy`. This will show you the logs of the service.

#### Checking setup logs

Before any of the components becomes available, a `setup.sh` script is run, which performs its initial setup (package download, configuration). The logs of this script are stored in `/var/log/defguard.log` on a corresponding EC2 instance. You can check this log file to see if there were any issues during the setup.
