# Gateway setup

## Binary Install

1. Checkout Gateway releases [here](https://github.com/DefGuard/gateway/releases) and download compatible binary from Github page.
2. Decompress and move to bin directory

```sh
tar xcf ./gateway.tar.gz
sudo chmod +x gateway
sudo mv gateway /usr/bin/
```
3. Start gateway
`gateway -g defguard.com:50055 -t <DEFGUARD_TOKEN>`

**Note** Don't forget to copy DEFGUARD_TOKEN necessary to start gateway connection from modal available on overview page under `Docker run command`


##  Docker-compose

To start your gateway using docker-compose:

1. We prepared a [git repository](https://github.com/DefGuard/deployment) with docker-compose configuration, clone it:

```
git clone git@github.com:DefGuard/deployment.git && cd deployment
```

2. Copy and fill in the .env file:

```
cp .env.template .env
```

Variables to set:

* `DEFGUARD_USERSPACE` , `-u` - Use userspace wireguard implementation, useful on systems without native wireguard support

* `DEFGUARD_GRPC_URL` , `-g <URL>` - Defguard server GRPC endpoint URL default is https://localhost:50055

* `DEFGUARD_STATS_PERIOD` ,`-p <SECONDS>` - Defines how often (seconds) should interface statistics be sent to Defguard server

* `DEFGUARD_TOKEN` ,`-t <TOKEN>` - Token received on Defguard after completing network wizard

> You can find list of all environment variables and arguments with explanation [here](../in-depth/environmental-variables-configuration.md).


3. Finally, run the service with docker-compose:

```
docker-compose up
```

If everything went well, your Gateway should be connected to Defguard and you can start [adding new devices to your network](community-features/wireguard/adding-wireguard-devices.md).

