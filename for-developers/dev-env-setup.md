# Development environment setup

Remember to clone defguard repository recursively (with protos):

```
git clone --recursive git@git.teonite.net:orion/core.git
```

## With docker-compose

Using docker-compose you can setup a simple stack with:

* backend
* database (postgres)
* vpn gateway
* device connected to the gateway

This way you'll have some live stats data to work with.

To do so follow these steps:

1. Migrate database and insert test network and device:

```
docker-compose run core init-dev-env
```

2. Run the application:
```
docker-compose up
```

## Cargo

To run backend without docker, you'll need:

* postgres database
* environment variables set

Run postgres with:

```
docker-compose up -d db
```

You'll find environment variables in .env file. Source them however you like (we recommend https://direnv.net/).
Once that's done, you can run backend with:

```
cargo run
```