# Updating and version compatibility

{% hint style="warning" %}
**Always back up your database before updating Core.** Core is the only component with persistent storage (PostgreSQL). See the [deployment overview](overview.md#backup) for backup instructions.
{% endhint %}

Defguard is composed of multiple components (Core, Edge, Gateway) that communicate over gRPC. Each component can be updated independently, provided the components remain compatible.&#x20;

When components attempt to establish a connection, Defguard automatically performs a version check. If an incompatibility is detected, the connection is refused.

It's recommended to always use the newest version of each service and update them all together to avoid incompatibility.

{% hint style="info" %}
All components should be kept on the **same major.minor version** (e.g., all on 2.0.x). Pre-release tags (`-rc1`, `-alpha1`) are stripped during version comparison, so a `2.0.0-rc1` Gateway is considered compatible with a `2.0.0` Core.
{% endhint %}

### Pre-release and development versions

To test upcoming releases, see the [pre-production and development releases](pre-production-and-development-releases.md) page for Docker\
tags, package downloads, and one-line install options.

### General update workflow

1. **Back up the database** (Core only)
2. **Read the migration guides** for the version you're upgrading to
3. **Update all components** (Core, Edge, Gateway) to the latest version
4. **Verify connectivity** — check component logs for gRPC connection success
5. **Check the Core UI** — incompatible components will be shown as disconnected

### Upgrade guides by deployment method

Choose the guide that matches how you deployed Defguard:

* [Docker / Docker Compose](docker-compose.md#upgrading)
* [Standalone packages (DEB, RPM)](standalone-package-based-installation/#upgrading-packages)
* [OVA / Virtual Appliance](ova.md#managing-and-updating-containers)

### Official GitHub Releases

Check the GitHub repositories for each service to find their newest releases and release notes:

* [Defguard Core](https://github.com/DefGuard/defguard/releases)
* [Defguard Proxy](https://github.com/DefGuard/proxy/releases)
* [Defguard Gateway](https://github.com/DefGuard/gateway/releases)
* [Defguard YubiBridge](https://github.com/DefGuard/YubiKey-Provision/releases)

### Firewall rules for version or license checking <a href="#firewall-rules-for-version-or-license-checking" id="firewall-rules-for-version-or-license-checking"></a>

In order to prevent any issues with update checking or license verification, please open on your firewall access to HTTPS for the following host:

| Domain            | IP           | Purpose                      |
| ----------------- | ------------ | ---------------------------- |
| pkgs.defguard.net | 185.33.37.43 | Updates & License validation |
