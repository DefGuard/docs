---
description: >-
  Here you can track changes made in more detail than on our GitHub release
  page: https://github.com/DefGuard/defguard/releases
---

# Changelog

## v0.6.1 - released 2023.07.26

### ✨ Purge Wireguard VPN old stats

For now, we support only 24h window into stats (more detailed stats we plan to do using OpenTelemetry and Prometheus exporter for devops/admins to configure their dashboards and all events stats according to their needs).

So a lot of stats data is gathered, not used. We need to purge them.&#x20;

#### Other changes

Please visit the [GithHub release page](https://github.com/DefGuard/defguard/releases/tag/v0.6.1).

## v0.6.0 - released 2023.07.25

### ✨ Multiple VPN locations

**Problem**

defguard right now only supports one VPN network/location and one gateway ([Wireguard VPN configuration microservice](architecture.md)) instance for that location.

**Solution**

Implement supporting multiple VPN networks/locations

* configuring/editing/deleting
* **allow only selected groups/users (currently we support two: All users & Admin) to connect to selected Location/VPN**
* displaying detailed statistics for each VPN/Location
* allowing users to download the configuration for each device for any Location/VPN network&#x20;

### ✨ Multiple Gateways' for High Availability/Failover

**Problem**

Some companies (including ours) have infrastructure requirements for their systems to support **high availability/failover** to secure business continuity. VPN for some companies (remote access to their systems) is critical. In defguard, the component responsible for configuring/managing VPN connections is the [gateway](https://github.com/DefGuard/gateway). In order to provide HA/Failover we would need to spawn a gateway on each firewall/router and defguard would need to support multiple gateways.

**Solution**

Support multiple instances of gateway service in order to provide **high availability/failover and** display the status of each gateway:

![](../.gitbook/assets/gw.png)

#### Other changes

Please visit the [GithHub release page](https://github.com/DefGuard/defguard/releases/tag/v0.6.0).
