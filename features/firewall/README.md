# Firewall

{% hint style="warning" %}
**Availability**

This feature requires a Business or Enterprise plan. See the [pricing page](https://defguard.net/pricing/) for details.
{% endhint %}

{% hint style="warning" %}
Access Control List feature is available starting with Defguard Core v1.3.0 and Defguard Gateway v1.3.0.

Defguard Gateway v1.3.0+ supports Linux machines with [NFTables](https://nftables.org/).\
Defguard Gateway v1.4.0+ supports FreeBSD, NetBSD, and macOS machines with Packet Filter (PF).
{% endhint %}

{% embed url="https://www.youtube.com/watch?v=Go4C4LWcVS4" %}

Defguard's Firewall module (also known as ACL - Access Control List) lets administrators control which users, groups, and devices can reach specific network resources over VPN. Rules are centrally managed in Core and pushed to gateways automatically.

### What is the Firewall

Firewall rules define access to network resources for VPN-connected users and devices. Each rule answers a single question: _**who is allowed to access this destination?**_

Rules are enforced at the gateway level. When a user connects to a VPN location and sends traffic to a resource, the gateway checks whether their device IP is permitted by a rule for that destination. If no rule covers the destination, the location's [default policy](./#enabling-the-firewall) applies.

### How rules work

Each firewall rule in Defguard is **self-contained**: it fully defines access for a single destination by generating two firewall rules on the gateway:

* An **ALLOW** rule that permits traffic from the specified sources to the destination.
* A **DENY** rule that blocks all remaining traffic to that destination.

This has three important consequences:

* **Order does not matter.** Rules do not depend on each other or on the sequence in which they appear in the list.
* **The default policy does not affect rules.** A rule produces the same result regardless of the location's default policy setting. See [Enabling the Firewall](./#enabling-the-firewall).
* **Rules are portable.** A rule assigned to multiple locations produces identical access behaviour on each.

When multiple rules cover overlapping destinations, their allowed sources combine. All **ALLOW** rules are evaluated before any **DENY** rule on the gateway, so traffic permitted by any matching rule for a destination will get through.

### Permissions and Restrictions

The source side of a rule is divided into two parts:

**Permissions** define who is granted access. The sources listed here form the **ALLOW** firewall rule. You can permit specific users, groups, and network devices, or use the **All X have access** toggle to permit all entities of a given type at once.

**Restrictions** define exceptions removed from the permitted set. Restricted sources are subtracted from the permitted set before the **ALLOW** rule is generated. For each entity type you can restrict all entities or only specific ones.

The final ALLOW rule covers:

```
(permitted sources) minus (restricted sources)
```

The **DENY** rule always blocks all remaining traffic to the destination - it is a blanket rule and does not reference specific sources.

{% hint style="warning" %}
If the subtraction produces an empty source list - for example, all permitted groups have also been restricted - no **ALLOW** rule is generated. The destination becomes completely blocked for everyone.

This is because creating an **ALLOW** rule without a specified source would allow all traffic.
{% endhint %}

### Default Policy

The default policy controls how traffic is handled when it does not match any rule's destination. It is configured per location as part of the Firewall setting (see [Enabling the Firewall](./#enabling-the-firewall)):

* **Default Allow** - users and devices can access all resources unless limited by a firewall rule.
* **Default Deny** - all traffic not explicitly allowed by a firewall rule will be blocked.

**The default policy has no effect on traffic that is covered by a rule.** Rules always produce their own **ALLOW** and **DENY** pair regardless of this setting.

### Rules and Locations

Each rule is assigned to one or more locations. A rule only applies to VPN traffic within its assigned locations. Alternatively, enabling the **All locations** toggle makes the rule apply everywhere - useful for organisation-wide access policies.

**Firewall rules govern resource access within a VPN tunnel. They do not control access to the VPN location itself.**

Location access - which users and groups can establish a connection to a given location - is managed separately through the location's [Allowed Groups](../wireguard/create-your-vpn-network/#allowed-groups) setting. A user must satisfy both conditions to reach a protected resource:

1. They must have VPN access to the location (Allowed Groups).
2. They must be permitted by a firewall rule for that destination.

### Enabling the Firewall

The Firewall is configured per location. To enable it, open the location's settings and set the **Firewall** option to one of:

* **Disable firewall** - the Firewall is off for this location. No rules are enforced and all traffic is permitted.
* **Default Allow** - the Firewall is active. Traffic to destinations not covered by any rule is permitted.
* **Default Deny** - the Firewall is active. Traffic to destinations not covered by any rule is blocked.

<figure><img src="../../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Default Deny** is the recommended setting for production environments. It ensures that only explicitly permitted traffic can reach resources, regardless of whether a rule has been defined for every destination yet.
{% endhint %}

### Anatomy of a rule

<figure><img src="../../.gitbook/assets/image (342).png" alt="" width="563"><figcaption></figcaption></figure>

#### Basic configuration

* **Rule name** - a descriptive label used to identify the rule in the list and in gateway firewall comments.
* **Locations** - one or more locations the rule applies to. Enable **All locations** to apply the rule across every location.
* **Enabled** - when disabled, the rule is not included in the firewall configuration pushed to gateways.

{% hint style="warning" %}
Disabling a deployed rule creates a **pending change**. The rule remains enforced on gateways until the pending change is deployed. See [Managing rules](./#managing-rules).
{% endhint %}

#### Destination

The destination section defines the network resource the rule applies to.

<figure><img src="../../.gitbook/assets/image (3).png" alt="" width="511"><figcaption></figcaption></figure>

**Add manual destination settings** - when checked, you define destination parameters directly in the form. When unchecked, only [Destinations](aliases-and-destinations.md) attached to this rule define the target.

When manual settings are enabled, all three destination fields - address, port, and protocol - are required. Each must be provided via explicit input, its **Any** toggle, or an [Alias](aliases-and-destinations.md) attached to the rule that covers that dimension.

* **Addresses/Ranges** - one or more IPv4/IPv6 addresses, CIDR ranges, or IP ranges, comma-separated. Examples: `10.1.1.0/24`, `10.2.1.10-10.2.2.100`, `fd00:1000::/64`. Enable **Any address** to match all destinations - useful when the rule should restrict protocols or ports regardless of destination IP.
* **Ports** - TCP/UDP ports or port ranges, comma-separated. Examples: `22`, `8000-9000`, `80, 443`. Enable **Any port** to match all ports.
* **Protocols** - TCP, UDP, or ICMP. Enable **Any protocol** to match all protocols.

**Aliases and Destinations** - reusable destination definitions that can be shared across multiple rules. An **Alias** contributes additional address, port, and protocol parameters to this rule's manual destination. A **Destination** acts as a fully independent target and generates its own **ALLOW** and **DENY** firewall rules. See [Aliases and Destinations](aliases-and-destinations.md) for full details.

{% hint style="info" %}
ACL rule destinations can also be used to **automatically populate Allowed IPs** in client WireGuard configs. See [Generate Allowed IPs from Firewall Rules](../wireguard/create-your-vpn-network/#generate-allowed-ips-from-firewall-rules) for details.
{% endhint %}

#### Permissions and Restrictions

<figure><img src="../../.gitbook/assets/image (4).png" alt="" width="512"><figcaption></figcaption></figure>

**Permissions** - define who is granted access to the destination.

For each entity type (users, groups, network devices), either enable the **All X have access** toggle to permit every entity of that type, or select specific entities. At least one permitted source must be specified.

**Restrictions** - define exceptions from the permitted set.

For each entity type, check **Limit access for X** to enable restrictions, then choose:

* **Exclude all** - remove all entities of that type from the permitted set.
* **Exclude specific** - remove selected entities from the permitted set.

### Managing rules

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

All changes to rules - creating, editing, disabling, and deleting - are staged as **Pending Changes** and do not take effect on gateways until explicitly deployed.

The rule list is split into two sections:

* **Deployed Rules** - rules currently active on their assigned locations.
* **Pending Changes** - staged changes awaiting deployment, including new rules, edits to existing rules, rules being disabled, and deletions.

Use the **Deploy pending changes** button to push all staged changes to affected gateways at once. Deploying in batches reduces the risk of transient firewall disruptions compared to applying changes one at a time.

{% hint style="warning" %}
**Eventual consistency** - Defguard does not track rule application status per individual gateway. If a gateway loses connectivity to Core at the time of deployment, the updated configuration will not be propagated immediately. Rules will be applied once the connection is restored.
{% endhint %}

### Examples

#### Granting access to specific users

**Goal:** Allow two specific users to reach an internal server at `10.1.1.50` through the _Office-Berlin_ location.

Create a rule assigned to _Office-Berlin_, with destination `10.1.1.50` and the two users selected in Permissions. Once deployed, those users' device IPs are permitted to reach `10.1.1.50`. All other traffic to that address is blocked by the **DENY** rule.

#### Using groups with exceptions

**Goal:** Allow all members of the _Staff-Berlin_ group to access `10.1.1.0/24`, except members of the _Contractors_ group.

Create a rule with destination `10.1.1.0/24`, the _Staff-Berlin_ group permitted, and a restriction excluding the _Contractors_ group. Members of _Contractors_ who are also in _Staff-Berlin_ are subtracted from the permitted set and will not appear in the **ALLOW** rule.

This approach scales well: adding a new employee to _Staff-Berlin_ grants them access automatically, and any contractor added to _Contractors_ is excluded without modifying the rule.

#### Using a Destination for a named service

**Goal:** Grant access to a PostgreSQL server at `10.2.0.38:5432` from multiple rules without repeating the address and port configuration in each.

First, create a Destination called _PostgreSQL - Analytics DB_ with address `10.2.0.38`, port `5432`, and protocol TCP. See [Aliases and Destinations](aliases-and-destinations.md).

Then add _PostgreSQL - Analytics DB_ in the Destinations field of any rule that should grant access to that server. Each rule that includes it gets its own independent **ALLOW** and **DENY** pair for `10.2.0.38:5432`, with sources taken from that rule's Permissions. Updating the Destination's address or port in one place updates it for every rule that uses it.

#### Restricting access by port and protocol

**Goal:** Allow all users to reach a web server at `10.3.0.10`, but only on port 443 over TCP - blocking all other ports and protocols on that address.

Create a rule with destination `10.3.0.10`, port `443`, protocol **TCP**, and **All users have access** enabled in Permissions. Because port and protocol are explicitly set, the **ALLOW** rule only permits TCP/443 traffic. The **DENY** rule blocks all other traffic to `10.3.0.10`, including other ports and protocols.

#### Using an Alias

**Goal:** Reuse a standard HTTPS port-and-protocol restriction across multiple rules without repeating the configuration.

Create an Alias called _HTTPS_ with port `443` and protocol TCP. See [Aliases and Destinations](aliases-and-destinations.md).

In a rule targeting `10.3.0.10`, add the _HTTPS_ alias with no ports or protocols set in the manual destination (the **Any address/Any port** toggles should be disabled to not override the alias). The alias contributes port `443` and protocol TCP to the rule's destination settings, making the effective destination `10.3.0.10:443/TCP`. The same _HTTPS_ alias can be added to any other rule to apply the same restriction there too.
