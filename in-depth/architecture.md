# Architecture Overview

Here you can see high level view of Defguard architecture with all it dependencies and their role.

![Defguard architecture](../.gitbook/assets/architecture.png)

### Basics

Core is a Rust web server which is exposed as REST API and GRPC web server with typescript and rust clients, it handles connection to database, ldap server and gateway core also handles user authorization via ldap account. It's configurable using Environmental Variables which you can find [here](environmental-variables-configuration.md).&#x20;

See detailed API docs [here](https://google.com).

Gateway is a small CLI GRPC client written in Rust which sends network statistics to Core server and apply network configuration changes on message from core.\
Our frontend is React app written in Typescript which allows handling all api calls via Web UI.\
See detailed GRPC docs [here](https://google.com)\


### Sample setup flow

After creating your network in our wizard and running our gateway program core will message it with network data. Gateway after receiving data will setup your network using wireguard commands you can think of it like a wrapper on wireguard commands which also sends network information through GRPC. After successfully setting up your network gateway will start sending your networks stats in period given as argument on gateway program start or if not provided at default which is 60 seconds. You can see all of your network statistics, connected users, bandwith, user devices on the overview page.

