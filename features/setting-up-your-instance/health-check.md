# Health check

## Core & Proxy

[Core](https://github.com/defguard/defguard) and [Proxy](https://github.com/defguard/proxy) provides health endpoint at  `GET /api/v1/health` which checks whether the application server is running. It does not verify the other services like [Gateway](gateway.md) and database are running.

\
Example request:

```bash
curl "https://defguard.com/api/v1/health" 
```

Example response:

```
alive
```

## Gateway

You can enable in gateway config ([example config](https://github.com/DefGuard/gateway/blob/main/example-config.toml)) a health check port, by adding the following line:

```
health_port = 55003
```

In this example gateway will open an additional HTTP port number 55003 and will return the following HTTP status codes:

* ```
  200 - Gateway is working and is connected to CORE
  ```
* ```
  503 - gateway works but is not connected to CORE
  ```

By default no healthcheck ports are open.
