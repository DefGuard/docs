# Docker setup

1. Make sure you have images for all our components which are available here:

- [Frontend](image_link)
- [Core server](image_link)
- [Gateway](image_link)

2. Make sure you have [Docker](https://www.docker.com/get-started/) and
   [Docker Compose](https://docs.docker.com/compose/install/) installed.

## Run all components in one docker-compose.yaml file

First create new directory for example **Defguard** and into this directory clone all our projects
<br>
Frontend

```
git clone git@github.com:DefGuard/frontend.git
```

Core server

```
git clone git@github.com:DefGuard/core.git
```

Gateway

```
git clone git@github.com:DefGuard/wireguard-gateway.git
```

In cloned Core server repository create directory named .volumes inside volumes directory create sqlite directory we need
it to store our Sqlite database in locally.

Next in created Defguard root repository create docker-compose.yaml this file will run core server, frontend, frontend docs services and gateway.

```yaml
version: "3"

services:
  core:
    build:
      context: .
      dockerfile: Dockerfile
    environment:
      DEFGUARD_JWT_SECRET: <YOUR_JWT_SECRET>
      DEFGUARD_DATABASE_URL: sqlite://sqlite/defguard.db
    volumes:
      - "./core/.volumes/sqlite:/app/sqlite"
    ports:
      # rest api
      - "8000:8000"
      # grpc
      - "50055:50055"
  web:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "4200:5000"
  ladle:
    build:
      context: ./frontend
      dockerfile: Dockerfile.ladle
    ports:
      - "4201:5000"
  docs:
    build:
      context: .
      dockerfile: ./frontend/Dockerfile.docs
    ports:
      - "4202:5000"
  gateway:
    image: registry.teonite.net/defguard/wireguard:latest
    environment:
      DEFGUARD_GRPC_URL: https://core:50055
      DEFGUARD_STATS_PERIOD: 60
      DEFGUARD_TOKEN: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJEZWZHdWFyZCIsInN1YiI6Im1vZGlmaWVkIiwiY2xpZW50X2lkIjoiIiwiZXhwIjo1OTQ5MDM0NTcxLCJuYmYiOjE2NTQwNjcyNzYsInJvbGVzIjpbXX0.Z8re0LmnE3xeL4z30CvFsCD2ETAkXIOpCfq1Q4axl3w
      RUST_LOG: debug
    ports:
      # wireguard endpoint
      - "50051:50051/udp"
    depends_on:
      - core
    cap_add:
      - NET_ADMIN
```

To start frontend and core server run

`docker-compose up -d`

To access your Defguard deployment go to `http://localhost:5000/` in your web browser then setup your Nginx server to redirect your domain to `localhost:5000`


If you want to update changes made in repository and rebuild your container go to repository run
`Git pull`

then 

`docker-compose down && docker-compose up -d --build`


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
