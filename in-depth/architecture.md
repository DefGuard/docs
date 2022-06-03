# Architecture  Overview

Here you can see high level view of Defguard architecture with all it dependencies and their role.

![Defguard architecture](../.gitbook/assets/architecture.png)

### Basics

Core is a Rust web servver which is exposed as REST Api, and GRPC server with Typescript frontend client and Rust GRPC client which allows to communicate with wireguard server.

See detailed REST api docs [here](https://google.com)\
See detailed GRPC docs [here](https://google.com)

Core is configurable by using environmental variables which defines all things like database url,\
server port and many more. You can see list list of all environmental variables [here](environmental-variables-configuration.md).

