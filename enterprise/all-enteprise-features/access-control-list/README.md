---
icon: user-shield
---

# Access Control List

{% hint style="info" %}
Access Control List feature is available in Defguard Core v1.3.0 and Defgaurd Gateway v1.3.0.

Defguard Gateway v1.3.0 supports Linux machines with [NFTables](https://nftables.org/).
Defguard Gateway v1.4.0 supports FreeBSD, NetBSD, and macOS machines with Packet Filter (PF).
{% endhint %}

The ACL (Access Control List) functionality in Defguard allows administrators to define and manage who can access specific network resources. It provides a clear and centralized way to control access based on users, groups, or devices, ensuring that only authorized entities can reach sensitive systems or services.

{% hint style="warning" %}
Access Control is an [enterprise feature](../../license.md). To be able to use it, it is requireed to [purchase a license](../../license.md#purchasing-the-license), or ensure your deployment does not [exceed the limits](../../license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

### How to enable Access Control List functionality

Access Control can be enabled for each location individually. To enable it:

1. Navigate to **VPN Overview** > **Edit Location settings**
2. In **Location configuration** section select **Enable ACL for this location**.
3. Click on **Save changes**.

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

**You should also set the default ACL policy for the location** (see below).

## Default Access Control List Policy

Default policy defines how to treat network traffic (with regarding to resources) that was not explicitly specified in ACL rules:

* **Allow** - users and devices connected to a location will be able to access all resources within the network, if the resource access is not modified by one of ACL rules.
* **Deny** - all traffic to network resources that is not regulated by one of the ACL rules will be blocked.

### How to define the default ACL Policy

Make sure ACL has been enabled (see above), otherwise the policy setting will not be inactive.

1. Navigate to **VPN Overview** > **Edit Location settings**
2. In **Location configuration** choose the desired option under **Default ACL Policy**.
3. Click on **Save changes**.

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

## List of ACL rules

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

**Access Control List** view displays all the rules defined in your system. The list is split into two sections.

**Deployed Rules** section displays the rules that have already been applied. Those rules should be in effect on relevant locations if the Gateway–Core connection is intact.

{% hint style="warning" %}
Defguard does not track rule application status per location. In the event of network connectivity issues between Gateway and Core components, rule propagation is not immediate. The system guarantees **eventual consistency**, meaning rules will be applied once the connection is restored.
{% endhint %}

**Pending Changes** section displays all the rules that have not yet been applied to locations. This includes:

* new rules
* modified rules
* deleted rules

Use the ![](<../../../.gitbook/assets/image (5).png>) button to apply all the rules from **Pending Changes** section.

{% hint style="info" %}

## Batch rule application

Defguard’s ACL functionality is designed to allow users to apply access control rules in batches. This approach minimizes the risk of transient network issues that could occur when deploying rules individually. By grouping changes and deploying them together, the system reduces the likelihood of connectivity hiccups or firewall disruptions.
{% endhint %}

The ACL list view also allows rule filtering by name, locations and other attributes

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

## How to add and modify ACL rules

To create a new rule, use the ![](<../../../.gitbook/assets/image (6).png>) button in the [ACL List View](./#list-of-acl-rules).

You can edit an existing rule by using the ![](<../../../.gitbook/assets/image (12).png>) context menu and selecting **"Edit"** in the [ACL List View](./#list-of-acl-rules)**.**

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption><p>Rule context menu</p></figcaption></figure>

### Anatomy of an ACL rule

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

The ACL form consists of three main sections:

#### Basic rule configuration

* rule name
* locations where the rule should be applied
* enabling /  disabling of the rule

{% hint style="info" %}
Each rule in Defguard can be **enabled** or **disabled** individually. When a rule is disabled, it remains stored in the system but is not applied to any locations, meaning it has no effect on access control until re-enabled. This allows administrators to temporarily deactivate rules without deleting them, making it easy to toggle access policies as needed.
{% endhint %}

#### Destination

This section is meant to define the resource to which access should be granted or restricted. Think of this section as the **"destination"** part of a firewall rule.

* IP addresses (IPv4 or IPv6) of the resources for which access will be granted or restricted. The addresses can be specified individually, by CIDR addresses (with a mask) or as a range. You can specify multiple comma-separated addresses. Examples of valid values for this field include:
  * `10.1.1.10, 10.1.2.0/24`
  * `10.2.1.10-10.2.2.100, fd00:1000::/64, fd00:1000::f0`
  * etc.
* Ports - TCP/UDP ports and port ranges
* Protocols that will be affected by the rule. All by default. Defguard ACL currently supports TCP, UDP and ICMP protocols.

#### Allowed and Denied sources

This section lets you define which traffic sources should be granted or denied access - essentially the **"source"** side of a firewall rule.

In Defguard, sources can be defined as one of three object types:

* Users
* User Groups
* Network Devices

Each ACL rule in Defguard is intended to fully define access to a specific resource, you must therefore always include at least one allowed source.

{% hint style="warning" %}
This setting is independent from the default location-level [**Allowed groups**](../../../admin-and-features/features-and-configuration/wireguard/create-your-vpn-network.md#allowed-groups) configuration.

If you give a user access to some resource through an ACL rule, but they do not have access to a given location, they still won't be able to access it, because they'll be unable to establish a VPN connection with the gateway.
{% endhint %}

### How to define your ACL ruleset

Access Control List (ACL) rules in Defguard are used to manage **who can access specific resources** across your network. Think of each rule as a clear instruction that says: _These users or devices are allowed to reach this resource – and optionally, these others are not._

#### Key Concepts:

* Each rule connects **who** (users, groups, or devices) to **what** (a resource address).
* At least one "allowed" source must always be specified - this defines who gets access.
* Optionally, you can **exclude** specific users, groups, or devices using the "denied" section.
* You can use this combination to create flexible rules, such as:\
  _Allow everyone in the “Remote Workers” group except a few individuals access specific office network._

This setup helps controlling access clearly and safely without worrying about lower-level network and firewall behavior.

#### Details

* ACL rules are **self-contained** – they fully define access for their target resource, are interpreted identically across all Gateways and are unaffected by the **default policy** location setting.
* **Default policy setting** at location level does not affect traffic covered by ACL rules. It applies only to traffic targeting addresses not matched by any ACL rule.
* A **destination address** is required in each rule – specifying only ports and/or protocols that are not allowed.
* **Ports and protocols** are optional. If specified, traffic is allowed _only_ on those ports/protocols; everything else is blocked.
* Each ACL results in two firewall rules:
  * An **ALLOW** rule for the allowed sources.
  * A **DENY** rule to block all other traffic to that destination.

### Examples

#### Allowing access for specific users

In this scenario we will allow specific users to access the 10.1.1.0/24 network, assuming the users connect through _Office-Berlin_ location.

To do this, the following new rules have to be added:

* Navigate to **Access Control**.
* Click on **Add new** button.
* Name the rule under **Rule Name**: _Staff access Berlin_.
* Select _Office-Berlin_ in the **Locations** input.
* Under **Manual Input** > **IPv4/v6 CIDR range or adderess**, enter: _10.1.1.0/24_.
* Add desired users in the **"Allowed Users/Groups/Devices** > **Users**.
* Click on the **Submit** button.

<figure><img src="../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

You will be redirected back to the [ACL List View](./#list-of-acl-rules) and the new rule should now be in the **Pending Changes** section.

<figure><img src="../../../.gitbook/assets/image (73).png" alt=""><figcaption></figcaption></figure>

Now, click on **Deploy pending changes (1)** button. After that, the rule should be applied on the _Office-Berlin_ location.

<figure><img src="../../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

#### Implementation details

##### Linux

All applied rules are deployed to Defguard Gateway. This means that the firewall on the Gateway that handles the _Office-Berlin_ location should contain appropriate [NFTables](https://nftables.org/) rules that implement the specified requirements. Let's see how this looks like in practice. Below is the `nftables list ruleset` output:

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

As you can see, Defguard has created a new inet-type table. This is to make sure Defguard's configuration won't interfere with your existing nftables.

The FORWARD chain specifies our rules. First you can see the `policy drop` default, which is a result of setting the **"default Deny"** policy in the location settings. Then the `established,related` line to skip reevaluation of established connections.

Finally the two lines that directly deal with our requirement to allow the two users into the network.

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

<figure><img src="../../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

## Gateway deployment with ACL

Under the hood, Access Control functionality uses [nftables](https://wiki.nftables.org/wiki-nftables/index.php/What_is_nftables%3F) to interact with the firewall and implement the rules. This means you'll need kernel version ≥ 5.10 to enable all kernel features required for proper operation.

### IP Forwarding

For traffic to flow between your network interfaces on Linux you may also need to enable IP forwarding, if you haven't done it already. This can be achieved by setting the `ip_forward` variable with the following command:

```
sysctl -w net.ipv4.ip_forward=1
```

If you want to make the change persistent, you will need to edit the `/etc/sysctl.conf` file and add the following line to it:

```
net.ipv4.ip_forward = 1
```

To load your changes in `sysctl.conf`, you can use `sysctl -p`.

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

### Forward chain priority

Defguard creates a forward chain in its namespace to control which forwarded packets are being allowed or blocked. This may interfere with your other nftables rules and chains.

```
chain FORWARD {
	type filter hook forward priority filter; policy deny;
	ct state established,related counter packets 119 bytes 13404 accept
}
```

By default this chain has the priority of `filter` (0). You can edit the priority by setting the `DEFGUARD_FW_PRIORITY` environment variable (or `fw_priority` config option) to chosen number, e.g. 1. The higher the priority, the later the chain runs in regard to your other forward chains.
