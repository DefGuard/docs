TODO

If on your wireguard server you don't want to run Gateway in docker container you can find pre-build binaries [here]("example.com")

**Note** As you need Token created on Defguard nafter network creation to start your gateway we recommend it to run it in separate
service using docker-compose or as a binary. Other possible option is to first run Frontend and Core create token then add gateway
service to our docker-compose.yaml

**docker-compose.yaml** For gateway
```yaml
version: "3"
services:
  gateway:
    image: registry.teonite.net/defguard/wireguard:latest
    environment:
      DEFGUARD_GRPC_URL: <URL_OF_YOUR_DEFGUARD_GRPC_SERVICE> # If it's on the same machine it's localhost:50055
      DEFGUARD_STATS_PERIOD: 60
      DEFGUARD_TOKEN: <DEFGUARD_TOKEN>
      RUST_LOG: debug
    ports:
      # wireguard endpoint
      - "50051:50051/udp"
    cap_add:
      - NET_ADMIN
```
