# Access Control List

Access Control is an [enterprise feature](../license.md). To use it you'll need to [purchase a license](../license.md#purchasing-the-license) or ensure your deployment does not [exceed the limits](../license.md#enterprise-is-free-up-to-certain-limits).

## How to enable Access Control List functionality

Access Control is enabled / disabled for each location individually. To enable it, go to location settings (you'll find the ![](<../../.gitbook/assets/image (15).png>) button on the [network overview](../../admin-and-features/features-and-configuration/wireguard/network-overview.md) page).

There are two relevant options in the **"Location configuration"** section:

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Select the checkbox to enable ACL for the location.

**You should also set the default ACL policy for the location.** Default policy defines what happens with traffic to resources that were not explicitly specified in your ACL rules:

* Allow - users / devices connected to the location will be able to access all resources within the network if the resource access is not modified by one of your ACL rules.
* Deny - all traffic to network resources that is not regulated by one of the ACL rules will be dropped

## List of ACL rules

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

**Access Control List** view displays all the rules defined in your system. The list is split into two sections.&#x20;

**"Deployed Rules"** section displays the rules that were already applied. Those rules should be in effect on relevant locations if the gateway-core connection is intact.

{% hint style="warning" %}
Defguard does not track rule application status per location. In the event of network connectivity issues between gateway and core components, rule propagation is not immediate. The system guarantees **eventual consistency** _-_ rules will be applied once the connection is restored.
{% endhint %}

**"Pending Changes"** section displays all the rules that have not yet been applied to locations. This includes:

* new created rules
* modified rules
* deleted rules

Use the ![](<../../.gitbook/assets/image (5).png>) button to apply all the rules from **"Pending Changes"** section.

{% hint style="info" %}
## Batch rule application

Defguard’s ACL functionality is designed to allow users to apply access control rules in batches. This approach minimizes the risk of transient network issues that could occur when deploying rules individually. By grouping changes and deploying them together, the system reduces the likelihood of connectivity hiccups or firewall disruptions.
{% endhint %}

The ACL list view also allows rule filtering by name, locations and other attributes

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## How to add and modify ACL rules

To create a new rule, use the ![](<../../.gitbook/assets/image (6).png>) button in the [ACL List View](access-control-list.md#list-of-acl-rules).&#x20;

You can edit an existing rule by using the ![](<../../.gitbook/assets/image (12).png>) context menu and selecting **"Edit"** in the [ACL List View](access-control-list.md#list-of-acl-rules)**.**

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption><p>Rule context menu</p></figcaption></figure>

### Anatomy of an ACL rule

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

The ACL form consists of three main sections:

**Basic rule configuration**

* rule name
* locations where the rule should be applied
* enabling /  disabling of the rule

{% hint style="info" %}
Each rule in Defguard can be **enabled** or **disabled** individually. When a rule is disabled, it remains stored in the system but is not applied to any locations, meaning it has no effect on access control until re-enabled. This allows administrators to temporarily deactivate rules without deleting them, making it easy to toggle access policies as needed.
{% endhint %}

**Destination**

This section is meant to define the resource to which access should be granted or restricted. Think of this section as the **"destination"** part of a firewall rule.

* IP addresses (IPv4 or IPv6) of the resources for which access will be granted or restricted. The addresses can be specified individually, by CIDR addresses (with a mask) or as a range. You can specify multiple comma-separated addresses. Examples of valid values for this field include:
  * `10.1.1.10, 10.1.2.0/24`
  * `10.2.1.10-10.2.2.100, fd00:1000::/64, fd00:1000::f0`&#x20;
  * etc.
* Ports - TCP/UDP ports, TODO
* Protocols that will be affected by the rule. All by default. Defguard ACL currently supports TCP, UDP and ICMP protocols.

**Allowed and Denied sources**

This section lets you define which traffic sources should be granted or denied access - essentially the **"source"** side of a firewall rule.

In Defguard, sources can be defined as one of three object types:

* Users
* User Groups
* Network Devices

Each ACL rule in Defguard is intended to fully define access to a specific resource, you must therefore always include at least one allowed source.

## How to define your ACL ruleset

Access Control List (ACL) rules in Defguard are used to manage **who can access specific resources** across your network. Think of each rule as a clear instruction that says: _“These users or devices are allowed to reach this resource - and optionally, these others are not.”_

### Key Concepts:

* Each rule connects **who** (users, groups, or devices) to **what** (a resource address).
* At least one "allowed" source must always be specified - this defines who gets access.
* Optionally, you can **exclude** specific users, groups, or devices using the "denied" section.
* You can use this combination to create flexible rules, such as:\
  &#xNAN;_&#x41;llow everyone in the “Remote Workers” group except a few individuals access to specific office network._

This setup helps you control access clearly and safely without worrying about lower-level network and firewall behavior.

### Details

* ACL rules are **self-contained -** they fully define access for their target resource, are interpreted identically across all gateways and are unaffected by the **"default policy"** location setting.
* **Default policy setting** at a location level does not affect traffic covered by ACL rules. It applies only to traffic targeting addresses not matched by any ACL rule.
* A **destination address** is required in each rule; specifying only ports and/or protocols is not allowed.
* **Ports and protocols** are optional. If specified, traffic is allowed _only_ on those ports/protocols—everything else will be blocked.
* Each ACL results in two firewall rules:
  * An **ALLOW** rule for the allowed sources.
  * A **DENY** rule to block all other traffic to that destination.

## Gateway setup for ACL

### Prerequisites

Under the hood, Access Control functionality uses [nftables](https://wiki.nftables.org/wiki-nftables/index.php/What_is_nftables%3F) to interact with the firewall and implement the rules. This means you'll need kernel version ≥ 5.10 to enable all kernel features required for proper operation.
