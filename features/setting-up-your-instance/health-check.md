# Health check

## Core & Proxy

### Rest Api

[Core](https://github.com/defguard/defguard) and [Proxy](https://github.com/defguard/proxy) provides health endpoint at  `GET /api/v1/health` which checks whether the application server is running.

Example request:

```bash
curl "https://defguard.example.com/api/v1/health" 
```

Example response:

```
alive
```

### gRPC

#### Proxy

To verify gRPC services for **Proxy** are alive, there is endpoint at `GET /api/v1/health-grpc` that verify it.

Example request:

```bash
curl "https://enroll.example.com/api/v1/health-grpc"
```

Possible responses:

```
200 - Proxy is working and is connected to CORE
503 - Proxy works but is not connected to CORE
```

#### Core

To check if core gRCP service is alive, we recommend to use community tools like [grpc\_health\_probe](https://github.com/grpc-ecosystem/grpc-health-probe).

Example request for core:

```sh
./grpc_health_probe -addr=defguard.example.com:50055
```

Example response for core:

```
status: SERVING
```

## Gateway

You can enable in gateway config ([example config](https://github.com/DefGuard/gateway/blob/main/example-config.toml)) a health check port, by adding the following line:

```
health_port = 55003
```

In this example gateway will open an additional HTTP port number 55003 and will return the following HTTP status codes:

* <pre><code><strong>200 - Gateway is working and is connected to CORE
  </strong></code></pre>
* ```
  503 - gateway works but is not connected to CORE
  ```

By default no healthcheck ports are open.
