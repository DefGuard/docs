---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/access-control-list/acl-aliases
---

# Aliases and Destinations

Aliases and Destinations are reusable destination definitions that can be shared across multiple firewall rules. Instead of repeating the same addresses, ports, and protocols in every rule, you define them once and reference them wherever needed.

They serve different purposes and behave differently when used in a rule:

* An **Alias** contributes its parameters to a rule's manual destination settings. Its addresses, ports, and protocols are merged with whatever you have defined directly in the rule, forming a single combined destination.
* A **Destination** acts as an independent target. When a rule references a Destination, that Destination generates its own separate **ALLOW** and **DENY** firewall rules on the gateway - with sources taken from the rule's Permissions - in addition to any rules generated from the rule's manual destination settings.

Use an **Alias** when you want to build a reusable component that adds to a rule's destination - for example, a standard set of ports for a well-known protocol. Use a **Destination** when you want to represent a specific named service or resource that should be independently protected - for example, a database server with a fixed address and port that multiple teams need access to under their own separate permission sets.

### Anatomy of an Alias

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="563"><figcaption></figcaption></figure>

* **Alias name** - a descriptive label used to identify the alias when selecting it in a rule's destination section.
* **Addresses/Ranges** - one or more IPv4/IPv6 addresses, CIDR ranges, or IP ranges, comma-separated. Examples: `10.1.1.0/24`, `10.2.1.10-10.2.2.100`.
* **Ports** - TCP/UDP ports or port ranges, comma-separated. Examples: `22`, `8000-9000`, `80, 443`.
* **Protocols** - TCP, UDP, or ICMP.

At least one of addresses, ports, or protocols must be provided. The remaining fields are optional - an alias that defines only ports and protocols with no addresses is valid, as is one that defines only addresses with no port or protocol restriction.

When an alias is added to a rule, its values are merged with the rule's manually configured destination parameters. For example, if a rule targets `10.3.0.10` with no ports or protocols set, and you add an alias defining port `443` and protocol TCP, the effective destination becomes `10.3.0.10:443/TCP`.

{% hint style="info" %}
Aliases always define explicit values. They do not have "any" toggles and cannot override a rule's address, port, or protocol scope - they can only add to it.
{% endhint %}

### Anatomy of a Destination

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

* **Destination name** - a descriptive label that identifies the service or resource, used when selecting it in a rule's destination section.
* **Addresses/Ranges** - one or more IPv4/IPv6 addresses, CIDR ranges, or IP ranges, comma-separated. Enable **Any address** to match all destinations.
* **Ports** - TCP/UDP ports or port ranges, comma-separated. Enable **Any port** to match all ports.
* **Protocols** - TCP, UDP, or ICMP. Enable **Any protocol** to match all protocols.

All three fields are required. Each must either have explicit values entered or its **Any** toggle enabled.

When a Destination is added to a rule it generates its own independent set of firewall rules on the gateway: an **ALLOW** rule with the rule's permitted sources and a blanket **DENY** for all other traffic, scoped to the Destination's address, ports, and protocols. This is separate from and in addition to any firewall rules generated from the rule's manual destination settings.

{% hint style="warning" %}
A Destination is linked to every rule that uses it. Modifying a deployed Destination and deploying the change will update the firewall rules for all of those rules at once. Review which rules reference a Destination before modifying it.
{% endhint %}

### Managing Aliases and Destinations

#### Lists

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Both Aliases and Destinations have separate list views accessible from the **Aliases** and **Destinations** navigation items. Each list is split into:

* **Deployed** - currently active and available for use in rules.
* **Pending** - modifications to existing aliases or destinations awaiting deployment.

#### Creating

New Aliases and Destinations are **immediately available** after creation - they do not require a deployment step. A newly created Alias or Destination has no effect on any firewall rules until it is explicitly added to a rule.

#### Modifying

Editing a deployed Alias or Destination creates a **pending change**. The modification does not take effect on the gateway until deployed using the **Deploy pending changes** button. Deploying pushes the updated definition to all rules that reference it and triggers a firewall configuration update on all affected gateways.

#### Deleting

An Alias or Destination can only be deleted if it is **not currently used by any rules**. If it is in use, the UI will show which rules reference it. Remove it from those rules first, then delete it.

Deletion is immediate and does not require a deployment step.
