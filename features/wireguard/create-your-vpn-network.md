# Create your VPN network

## Before we start

* Make sure your Defguard instance is [deployed](../setting-up-your-instance/) and running
* Make sure you downloaded our [Wireguard Gateway](https://github.com/DefGuard/gateway)
* Make sure you know what [Wireguard](https://www.wireguard.com/) is and have basic understanding of how it works

## Network creation wizard

After successful deployment of your Defguard instance first screen you'll see will look like this

![First screen after logging to your freshly installed instance](../../.gitbook/assets/Wizard.png)

### Network name and type

Nothing fancy in this step just pick your network name and type

![First step on network creation wizard](../../.gitbook/assets/wizardstep1.png)

{% hint style="info" %}
Currently only supported network type is regular but we hope it'll change in a short future
{% endhint %}

### Network configuration

After choosing a name for your network you"ll be on to the next step which is network configuration this is where things might feel a little bit complicated but no worries it be as painless as possible.

![Network configuration setup](../../.gitbook/assets/wizardstep2.png)

### Let's briefly discuss all of these weird inputs

### **Address**

It’s the IP address of the network interface that Wireguard sets up for the peer.

The IP address for a WireGuard interface is defined with a network prefix, which tells the local host what other IP addresses are available on the same virtual subnet as the interface. In the above placeholder, this prefix is /24. That indicate to the localhost that other addresses in the same /24 block as the address itself (10.1.1.0 to 10.1.1.255) are routable through the interface.

### Network port

Port on which Wireguard listens on the gateway

### Gateway endpoint

It's IP address of your [gateway](https://github.com/DefGuard/gateway) in Wireguard words Endpoint is the remote peer's "real" IP address and port, outside of the WireGuard VPN. This setting tells the localhost how to connect to the remote peer in order to set up a WireGuard tunnel.

{% hint style="warning" %}
Network port and gateway endpoint port can be different if Wireguard server is behind NAT/Firewall
{% endhint %}

### Optional settings:

### Allowed IPs

is the set of IP addresses the localhost should route to the remote peer through the WireGuard tunnel. This setting tells the localhost what goes in a tunnel.

### DNS

DNS specifies DNS resolvers to query when the Wireguard interface is up and domains to search for unqualified hostnames.

{% hint style="info" %}
No worries if you get something wrong you can always change it later
{% endhint %}

## What to do after wizard completion?

After completing all steps from above you will be redirected to Network overview page which detect if your gateway is connected or not if your gateway never connected to Defguard you'll see modal looking like this

![Modal with docker command to copy to start your gateway server](../../.gitbook/assets/rungatewaymodal.png)

### Wireguard Gateway setup

Wireguard Gateway is a client program which connects to Defguard in order to setup your network and send all information about your network and it's users. You can think about it like waiter which delivers food(data eg. stats, configuration) between client(Defguard server) and server(wireguard server) and takes orders from Defguard server to setup your network.

#### First run

After creating your network on Defguard you'll see modal pop-up as in picture above with docker run command to start your gateway server but there are other options to start your server.

> If you want to use above command make sure you have installed [docker](https://www.docker.com/).

Second option is downloading source code from this [repository](https://github.com/DefGuard/gateway) and building it using\
`cargo build --release` command then you'll find binary file named `gateway` at `target/release` directory

Or if you don't want to build it yourself, you can find prebuild binaries [here](https://github.com/DefGuard/gateway/releases).

#### Environmental variables / Arguments

If you're using docker image you can pass this value as environmental variables on binary you can pass them as arguments

`DEFGUARD_USERSPACE` , `-u` - Use userspace wireguard implementation, useful on systems without native wireguard support

`DEFGUARD_GRPC_URL` , `-g` - Defguard server gRPC endpoint URL default is https://localhost:50055

`DEFGUARD_STATS_PERIOD` ,`-p` - Defines how often (seconds) should interface statistics be sent to Defguard server

`DEFGUARD_TOKEN` ,`-t` - Token received on Defguard after completing network wizard

## VPN feature in depth

If you think something above is complicated or unclear you might want to take a look at our [in depth Wireguard VPN explanation](../../in-depth/wireguard-vpn.md).



