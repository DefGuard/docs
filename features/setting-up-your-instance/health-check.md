# Health check

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
