# User SNAT bindings

{% hint style="warning" %}
This is an enterprise feature. To use it, purchase our [enterprise license](enterprise/license.md) or ensure that your deployment does not exceed the [usage limits](enterprise/license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

{% hint style="info" %}
This feature is available starting from version 1.5
{% endhint %}

**User SNAT bindings** allow administrators to assign specific public IP addresses to users for outbound traffic from Defguard VPN gateways in a given location. This provides fine-grained control over how user traffic appears to external networks.

## Overview

Defguard administrators are able to create SNAT (Source Network Address Translation) bindings which include following information:

* location ID
* user ID
* public IP address

{% hint style="warning" %}
Each user can have only one binding configured in a given location.
{% endhint %}

Once a binding is configured, when a specified user connects to a given location using one of their devices, all their traffic leaving the VPN gateway (for example to access the public internet) will appear as if originating from the configured IP address.

## Configuring user SNAT bindings

Bindings can be configured through the Defguard REST API, which supports following operations:

* listing all bindings for a given location
* creating a new binding
* updating an existing binding
* removing an existing binding

All the endpoints require authentication (with session cookies or [API tokens](integrations/api-tokens.md#generating-api-token)) and Admin role permissions.

The details are available in the [Swagger docs](integrations/api-tokens.md#rest-api-documentation) provided by each Defguard instance.

Each modification to configured bindings triggers an update to be sent to relevant VPN gateways assigned to a given location.

## Implementation details

{% hint style="warning" %}
Currently SNAT binding functionality is only supported on Linux gateways.
{% endhint %}

Configured bindings are sent to relevant VPN gateways as part of their firewall configuration.

Each binding is then translated into one SNAT rule in the `POSTROUTING` firewall chain.

Those rules are applied before the final `MASQUERADE` rule which handles all the remaining outbound traffic.

The gateway itself is not aware of public IPs available on its network interfaces, so it's important to verify that the IP address assigned to a user is indeed available. Otherwise, it's possible to block all outgoing traffic for a user.
