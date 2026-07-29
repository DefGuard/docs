# Health check

## Edge

[Edge](https://github.com/defguard/proxy) provides health endpoint at `GET /api/v1/health` which checks whether the application is running.

Example request:

```sh
curl https://enroll.example.com/api/v1/health
```

Response:

* `alive` with status code 200 – Edge is working

To verify gRPC services for **Edge** are alive, there is endpoint at `GET /api/v1/health-grpc` that verify it.

Example request:

```sh
curl https://enroll.example.com/api/v1/health-grpc
```

Response:

* `alive` with status code 200 – Edge is working and is connected to Core
* `Not connected to Defguard Core` with status code 503 – Edge is working, but is not connected to Core

## Core

To check if [**Core**](https://github.com/defguard/defguard) is working, you can use endpoint at `GET /api/v1/health` which verify it.

Example request:

```sh
curl https://defguard.example.com/api/v1/health
```

Response:

* `alive` with status code 200 – Core is working

To check if core gRPC service is alive, we recommend to use community tools like [grpc\_health\_probe](https://github.com/grpc-ecosystem/grpc-health-probe).

Example request for Core:

```sh
grpc_health_probe -addr=defguard.example.com:50055
```

Example response:

```
status: SERVING
```

## Gateway

In Gateway configuration, a health check port can be enabled by adding the following line (see also [example config](https://github.com/DefGuard/gateway/blob/main/example-config.toml)):

```toml
health_port = 55003
```

In this example, Gateway will open an additional HTTP port number 55003. Now we can use `GET /health` endpoint to verify whether Gateway is working correctly.

If running in Docker you can also enable it by setting the `HEALTH_PORT` [environment variable](configuration.md#gateway-deployment-parameters).

By default the HTTP server will listen on all interfaces, but if you prefer to bind only a specific IP you can set it by using the `http_bind_address` config option (or `DEFGUARD_HTTP_BIND_ADDRESS` environment variable). For example:

```toml
http_bind_address = 10.0.10.20
```

Example request:

```sh
curl http://gateway.example.com:55003/health
```

Response:

* `alive` with status code 200 – Gateway is working and is connected to Core
* `Not connected to core` with status code 503 – Gateway is working but is not connected to Core

By default, no health check ports are open.

{% hint style="info" icon="burst-new" %}
Starting from version 2.0, the connection between Edge and Core is secured with mutual TLS (mTLS). The `health-grpc` endpoint still reports connectivity status, but a failure may also indicate a certificate mismatch rather than a network issue.
{% endhint %}
