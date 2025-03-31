---
icon: user-shield
---

# Access Control List

The ACL (Access Control List) functionality in Defguard allows administrators to define and manage who can access specific network resources. It provides a clear and centralized way to control access based on users, groups, or devices, ensuring that only authorized entities can reach sensitive systems or services.&#x20;

{% hint style="warning" %}
Access Control is an [enterprise feature](../license.md). To use it you'll need to [purchase a license](../license.md#purchasing-the-license) or ensure your deployment does not [exceed the limits](../license.md#enterprise-is-free-up-to-certain-limits).

Access Control is available in Defguard version ≥ 1.3.0 and Gateway version ≥ 1.3.0
{% endhint %}

## How to enable Access Control List functionality

Access Control is enabled / disabled for each location individually. To enable it, go to location settings (you'll find the ![](<../../.gitbook/assets/image (15).png>) button on the [network overview](../../admin-and-features/features-and-configuration/wireguard/network-overview.md) page).

There are two relevant options in the **"Location configuration"** section:

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Select the checkbox to enable ACL for the location.

**You should also set the default ACL policy for the location.**

Default policy defines what happens with traffic to resources that were not explicitly specified in your ACL rules:

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

* new rules
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

### How to define your ACL ruleset

Access Control List (ACL) rules in Defguard are used to manage **who can access specific resources** across your network. Think of each rule as a clear instruction that says: _“These users or devices are allowed to reach this resource - and optionally, these others are not.”_

#### Key Concepts:

* Each rule connects **who** (users, groups, or devices) to **what** (a resource address).
* At least one "allowed" source must always be specified - this defines who gets access.
* Optionally, you can **exclude** specific users, groups, or devices using the "denied" section.
* You can use this combination to create flexible rules, such as:\
  &#xNAN;_&#x41;llow everyone in the “Remote Workers” group except a few individuals access to specific office network._

This setup helps you control access clearly and safely without worrying about lower-level network and firewall behavior.

#### Details

* ACL rules are **self-contained -** they fully define access for their target resource, are interpreted identically across all gateways and are unaffected by the **"default policy"** location setting.
* **Default policy setting** at a location level does not affect traffic covered by ACL rules. It applies only to traffic targeting addresses not matched by any ACL rule.
* A **destination address** is required in each rule - specifying only ports and/or protocols is not allowed.
* **Ports and protocols** are optional. If specified, traffic is allowed _only_ on those ports/protocols—everything else will be blocked.
* Each ACL results in two firewall rules:
  * An **ALLOW** rule for the allowed sources.
  * A **DENY** rule to block all other traffic to that destination.

### Examples

#### Allowing access for specific users

In this scenario we will allow specific users to access the 10.1.1.0/24 network if they are connecting via the **"office-berlin"** location.

Do do this you will add new rule with:

* office-berlin location selected in the **"Locations"** input
* **"10.1.1.0/24"** CIDR in the destination input
* Appropriate users in the **"Allowed Users/Groups/Devices->Users"** input

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

Hit the ![](<../../.gitbook/assets/image (72).png>) button. You will be redirected back to the [ACL List View](access-control-list.md#list-of-acl-rules) and the new rule should now be in the **"Pending Changes"** section.

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

Now just hit the ![](<../../.gitbook/assets/image (74).png>) button and the rule should be applied on the **"office-berlin"** location.

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

**Under the hood**

You just deployed the rule to gateway. This means that the firewall on the gateway that handles the **"office-berlin"** location should contain appropriate nftables rules that implement the requirements you specified. Let's see what this looks like in practice. Below is the `nftables list ruleset` output:

```
...
table inet DEFGUARD {
        chain FORWARD {
                type filter hook forward priority filter; policy drop;
                ct state established,related counter packets 0 bytes 0 accept
                ip saddr { 10.100.200.155-10.100.200.156 } ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 accept comment "ACL 132 - Office access Berlin ALLOW"
                ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 drop comment "ACL 132 - Office access Berlin DENY"
        }
}
...
```

As you can see, Defguard has created a new inet-type table. This is to make sure Defguard's configuration won't interfere with your existing nftables.&#x20;

The FORWARD chain specifies our rules. First you can see the `policy drop` default, which is a result of setting the **"default Deny"** policy in the location settings. Then the `established,related` line to skip reevaluation of established connections.

Finally the two lines that directly deal with our requirement to allow the two users into the network.&#x20;

```
ip saddr { 10.100.200.155-10.100.200.156 } ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 accept comment "ACL 132 - Office access Berlin ALLOW"
```

This rule specifies two addresses as the **"traffic source" -** `10.100.200.155` and `10.100.200.156` . Those happen to be device addresses of our two users in the Wireguard VPN network that the gateway manages for this Location. The destination address **"10.1.1.0/24"** is exactly the network address we specified in the rule. And finally the **"accept"** verdict. All together, this rule allows the traffic specified in the UI to the network.

You may also notice that Defguard added a comment to the rule. The comment includes rule name so that it is easy for you to find the rule using tools like grep etc.

Finally, the last line:

```
ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 drop comment "ACL 132 - Office access Berlin DENY"
```

This line effectively blocks all other traffic to the 10.1.1.0/24 network. As mentioned earlier, the ACL rules in Defguard are self-contained and fully define access for their target resource. This set of rules can now be deployed to any gateway, no regardless of the **"default policy"** setting, and they will effectively do the same thing.

#### Adding access exceptions for specific users

Let's build on the last example. The example defined a single rule that grants access to a network to two users. In this example we will block access for one specific user. But first let's rethink our approach.

You may be tempted to specify the access for each user individually like we did while constructing the first rule. This may work at first or if your users don't change too often. But what if you have a constant influx of new users? This might get tedious pretty fast.

So what we will do is:

* we will define two groups
  * staff-berlin
  * externals
* we will add all the users that work in our "Berlin" office to staff-berlin group
* we will add all users we collaborate with in Berlin, but are not our direct employees, to the "externals" group
* we will allow all users in staff-berlin group access to the network
* we will add an exception for the users in "externals" group so that they are not allowed to access the network

Once you have created appropriate groups and assigned the users, let's update the ACL rule. The rule should now:

* still be assigned to the **"office-berlin"** location
* still define the destination resource address as "10.1.1.0/24"
* instead of specific users in the **"Allowed Users"** input we now select the **"staff-berlin"** group in the **"Allowed Groups"** input
* in **"Denied Groups"** input we should now select the **"externals"** group

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

## Gateway deployment with ACL

Under the hood, Access Control functionality uses [nftables](https://wiki.nftables.org/wiki-nftables/index.php/What_is_nftables%3F) to interact with the firewall and implement the rules. This means you'll need kernel version ≥ 5.10 to enable all kernel features required for proper operation.

### Masquerade

Masquerading between network interfaces falls outside the scope of Defguard’s responsibilities and must be handled by the system administrator. If your environment doesn’t already provide proper routing between the gateway’s interfaces, you may need to enable masquerading to ensure seamless communication.

As a shortcut, Defguard Gateway offers the `--masquerade` flag (or the `DEFGUARD_MASQUERADE=true` environment variable), which applies source NAT between all interfaces automatically, saving you from manually configuring masquerade rules at the system level. It results in this masquerade nftables rule:

```
    chain POSTROUTING {
            ...
            oifname != "lo" counter packets 4 bytes 240 masquerade
    }
```

{% hint style="warning" %}
The `--masquerade` option applies masquerading between **all** interfaces on the gateway, which may be more permissive than necessary in some environments. While convenient, this broad behavior might not align with more restrictive or segmented network designs. For greater control and tighter security, we recommend that administrators configure masquerading manually between only the interfaces that require it.
{% endhint %}
