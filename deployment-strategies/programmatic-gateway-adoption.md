# Programmatic gateway adoption

A Gateway can be programmatically adopted by calling the Defguard API. This is useful if you plan to add many Gateways and doing it one by one would be time-consuming.

Here is an example `curl` command that adopts a Gateway for a network with ID `42`.

```bash
curl -X POST 'https://defguard.example.com/api/v1/network/42/gateways/adopt' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer <your_api_token>' \
  -d '{
    "name": "prod-gateway-01",
    "ip_or_domain": "10.0.0.5",
    "grpc_port": 50066
  }'
```

Here, `ip_or_domain` is the address of the Gateway, and `grpc_port` is the Gateway gRPC port (`50066` by default). To authorize the request, first obtain an [API token](../features/integrations/api-tokens.md#generating-api-token) and replace `<your_api_token>` with it.

For more information about using the Defguard API, see [REST API documentation](https://docs.defguard.net/features/integrations/api-tokens#rest-api-documentation).

