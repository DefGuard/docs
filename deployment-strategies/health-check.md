---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/health-check
---

# Health check

## Proxy

[Proxy](https://github.com/defguard/proxy) provides health endpoint at `GET /api/v1/health` which checks whether the application is running.

Example request:

```sh
curl "https://enroll.example.com/api/v1/health"
```

Response:

```
"alive" - with status code 200 - Proxy is working
```

To verify gRPC services for **Proxy** are alive, there is endpoint at `GET /api/v1/health-grpc` that verify it.

Example request:

```bash
curl "https://enroll.example.com/api/v1/health-grpc"
```

Response:

```
"alive" with status code 200 - Proxy is working and is connected to CORE
"Not connected to Defguard Core" - with status code 503 - Proxy is working but is not connected to CORE
```

## Core

To check if [**Core**](https://github.com/defguard/defguard) is working, you can use endpoint at `GET /api/v1/health` which verify it.

Example request:

```bash
curl "https://defguard.example.com/api/v1/health"
```

Response:

```
"alive" - with status code 200 - Core is working
```

To check if core gRPC service is alive, we recommend to use community tools like [grpc\_health\_probe](https://github.com/grpc-ecosystem/grpc-health-probe).

Example request for core:

```sh
./grpc_health_probe -addr=defguard.example.com:50055
```

Example response:

```
status: SERVING
```

## Gateway

You can enable in gateway config ([example config](https://github.com/DefGuard/gateway/blob/main/example-config.toml)) a health check port, by adding the following line:

```toml
health_port = 55003
```

In this example, gateway will open an additional HTTP port number 55003. Now we can use `GET /api/v1/health` endpoint to verify whether gateway is working correctly.

If running in Docker you can also enable it by setting the `HEALTH_PORT` [environment variable](configuration.md#environmental-variables-arguments).

By default the HTTP server will listen on all interfaces, but if you prefer to bind only a specific IP you can set it by using the `http_bind_address` config option (or `DEFGUARD_HTTP_BIND_ADDRESS` environment variable). For example:

```toml
http_bind_address = 10.0.10.20
```

Example request:

```sh
curl "http://gateway.example.com:55003/api/v1/health"
```

Response:

```
"alive" - with status code 200 - Gateway is working and is connected to CORE
"Not connected to core" - with status code 503 - Gateway is working but is not connected to CORE
```

By default, no health check ports are open.
