# Architecture Overview

Here you can see high level view of Defguard architecture with all it dependencies and their role.

![Defguard architecture](../.gitbook/assets/architecture.png)

### Basics

Core is a Rust web server which is exposed as REST Api, and GRPC server it handles connection to database, ldap server and gateway. Gateway is a small CLI GRPC client written in Rust which sends network statistics to Core server and apply network configuration changes on message from core.\
Our frontend is React app written in Typescript.

See detailed REST api docs [here](https://google.com)\
See detailed GRPC docs [here](https://google.com)\


Core is configurable by using environmental variables which defines all things like database url,\
server port and many more. You can see list list of all environmental variables [here](environmental-variables-configuration.md).

### Sample setup flow

After creating your network in our wizard and running our gateway program core will message it with network data. Gateway after receiving data will setup your network using wireguard commands you can think of it like a wrapper on wireguard commands with posibillity to sends network information through GRPC. After successfully setting up your network gateway will sends to core Networks stats in period given as argument on gateway program start or if not provided at default which is 60 seconds. You can see all of your network statistics, connected users, bandwith, user devices on the overview page.

