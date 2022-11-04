# Architecture Overview

Below you can see Defguard architecture in [C4 model](https://c4model.com/) divided into context, containers, components and code.


## Context 
![Context look at Defguard architecture ](puml/architecture-context.svg)


## Containers
![Containers look at Defguard architecture ](puml/architecture-containers.svg)

## Components
![Components look at Defguard architecture ](puml/architecture-components.svg)


### Basics

Core is a Rust web server which is exposed as REST API and GRPC web server with typescript and rust clients, it handles connection to database, LDAP server and gateway. Core also handles user authorization via LDAP account. It's configurable using Environmental Variables which you can find [here](environmental-variables-configuration.md).

See Rustdocs [core](https://google.com) [gateway](https://google.com).

Gateway is a small CLI GRPC client written in Rust which sends network statistics to Core server and apply network configuration changes on message from core.\
Our frontend is React app written in Typescript which allows handling all API calls via Web UI.\
See detailed GRPC docs [here](https://google.com)\


### Example setup flow

After creating your network in our wizard and running our gateway program core will message it with network data. Gateway after receiving data will setup your network using wireguard commands you can think of it like a wrapper on wireguard commands which also sends network information through GRPC. After successfully setting up your network gateway will start sending your networks stats in period given as argument on gateway program start or if not provided at default which is 60 seconds. You can see all of your network statistics, connected users, bandwidth, user devices on the overview page.

