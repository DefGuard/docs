# Environment setup

Clone [Defguard Core repository](https://github.com/DefGuard/defguard) recursively (including Git submodules like protos and UI):

```
git clone --recursive git@github.com:DefGuard/defguard.git
```

## With docker-compose

Using [Docker Compose](https://docs.docker.com/compose/) you can setup a simple stack with:

* Defguard Core
* [PostgreSQL](https://www.postgresql.org/) database
* Defguard Gateway
* example device connected to the gateway

This way you'll have some live stats data to work with.

To do so follow these steps:

1. Migrate database and insert test network and device:

```
docker compose run core init-dev-env
```

2. Run the application:

```
docker compose up
```

### Switching images

To use different versions of Defguard images, edit _docker-compose.yaml_ file, replacing `image:` sections. Consult [Defguard Package](https://github.com/DefGuard/defguard/pkgs/container/defguard) versions to browse for available image tags.

For example, to use current development version, change this section in _docker-compose.yaml_:

```
core:
  image: ghcr.io/defguard/defguard:dev
```

### Running local code

To run local code you will need to build core image from local changes:

```bash
docker compose build core
```

Then just run the compose normally.

```bash
docker compose up
```

## Cargo

To run Defguard Core without Docker, you'll need:

* [PostgreSQL](https://www.postgresql.org/) database
* [Protobuf](https://protobuf.dev/) compiler (`protoc`)
* [NodeJS](https://nodejs.org/)
* environment variables set

The procedure to start Defguard Core:

1. Launch PostgreSQL database, for example using [Docker](https://www.docker.com/):

```
docker-compose up -d db
```

2. Install [pnpm](https://pnpm.io/)

```
sudo npm i -g pnpm
```

or use another method described in [pnpm installation](https://pnpm.io/installation/).

3. Build front-end

```
pushd web
pnpm install
pnpm build
popd
```

4. Start Defguard Core in development mode

You'll find environment variables in _.env_ file. Source them however you like (we recommend [direnv](https://direnv.net/)).

Once that's done, you can run backend with:

```
cargo run
```

5. Use a web browser to connect to Defguard. For example, when using the default configuration the web site should be accessible under this address:

**http://localhost:8000/**

### Minimum required settings

Consult [Configuration](../features/setting-up-your-instance/configuration.md) manual for a list of all available configuration settings.

* `DEFGUARD_COOKIE_INSECURE=true` - running HTTP server locally does not need secured cookies
* `DEFGUARD_SECRET_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` - 64-character long security key
* `DEFGUARD_LOG_LEVEL=debug` - increase logging level

## Frontend

{% hint style="info" %}
The domain used to access the frontend instance has to match with the cookie domain address (which can be set using DEFGUARD\_COOKIE\_DOMAIN).

For example, if the cookie domain is set to the default value of localhost, you should access frontend using localhost domain.
{% endhint %}
