# Setting up your instance

Defguard comes with three main components:

- Core service: webapp database
- VPN gateway server: retrieves configuration from core and configures VPN interfaces on the gateway server
- Provisioning station: client application which can be started on any pc to auto generate PGP keys for YubiKey

Use your preferred method to deploy Defguard core service

- [Docker Compose](docker-compose.md)
- [Kubernetes](kubernetes.md)

Client services

- [Gateway](gateway.md)
- [YubiBridge](enteprise-features/yubikey-provisioning.md)

--- 

### Tips
See our [Configuration](in-depth/environmental-variables-configuration.md) document to check all configurable things before you start.\
And learn about our Architecture [**here**](in-depth/architecture.md) to see how it works.
