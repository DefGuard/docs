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
git clone --recursive https://github.com/DefGuard/gateway.git && cd gateway
```

2. Copy and fill in the .env file:

```
cp .env.template .env
```

3. Finally, run the service with docker-compose:

```
docker-compose up
```

If everything went well, your Gateway should be connected to Defguard and you can start [adding new devices to your network](features/wireguard/adding-wireguard-devices.md).

## SSL

You'll need a valid CA certificate to connect Gateway to Defguard.
See [SSL instructions](https://defguard.gitbook.io/defguard/features/setting-up-your-instance/docker-compose#ssl-setup).
