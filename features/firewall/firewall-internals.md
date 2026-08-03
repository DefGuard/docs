---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/access-control-list/firewall-internals
---

# Implementation Details

{% hint style="info" %}
The examples in this page relate to the scenarios described in the main [Firewall](./) documentation.
{% endhint %}

### Firewall Interaction

Defguard Gateway does **not** take control of the entire firewall. Instead, dedicated chains (in NFTables) and anchors (in PF) are used as not to interfere with other rules on the firewall.

### NFTables (Linux)

{% hint style="info" %}
NFTables support requires Linux kernel version ≥ 5.10.
{% endhint %}

Under the hood, the Firewall feature uses [NFTables](https://wiki.nftables.org/wiki-nftables/index.php/What_is_nftables%3F) to implement firewall rules on Linux gateways.

Defguard creates a dedicated table per network interface. For a gateway managing an interface named `wg0`, the table is named `DEFGUARD-wg0`. This keeps Defguard's rules isolated from any existing NFTables configuration on the host.

To inspect the rules applied by Defguard, run on the gateway host:

```
nft list table inet DEFGUARD-wg0
```

Or to see all tables at once:

```
nft list ruleset
```

#### Example

Consider a rule named _Staff access, Berlin_ (ID 132) that permits two specific users to access the `10.1.1.0/24` network on the _Office-Berlin_ location, with the location's default policy set to **Default Deny**.

After deploying this rule, the output of `nft list table inet DEFGUARD-wg0` should look similar to the following:

```
table inet DEFGUARD-wg0 {
    chain FORWARD {
        type filter hook forward priority filter; policy drop;
        ct state established,related counter packets 0 bytes 0 accept
        ip saddr { 10.100.200.155, 10.100.200.156 } ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 accept comment "RULE 132 - Staff access, Berlin ALLOW"
        ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 drop comment "RULE 132 - Staff access, Berlin DENY"
    }
}
```

As you can see, Defguard has created a new table of type _inet_ (the one that handles both IP v4 and v6 addresses).

The `policy drop` on the FORWARD chain corresponds to the **Default Deny** location setting. The `ct state established,related` line allows return traffic for already-established connections.

The **ALLOW** rule matches traffic from the two users' device IPs in the WireGuard VPN subnet (`10.100.200.155` and `10.100.200.156`) to the destination network, and accepts it. The **DENY** rule matches all remaining traffic to `10.1.1.0/24` and drops it - regardless of source. Together these two rules make the firewall rule self-contained: it behaves identically on any gateway regardless of the default policy setting.

Defguard adds a comment to each rule containing the rule ID and name, making it easy to find corresponding rules using tools like `grep`:

```
nft list ruleset | grep "RULE 132"
```

#### Example with port and protocol restriction

For a rule that restricts access to TCP port 443 only, the **ALLOW** rule also includes a protocol and port match:

```
meta l4proto tcp dport { 443 } ip saddr { 10.100.200.155, 10.100.200.156 } ip daddr { 10.3.0.10 } counter packets 0 bytes 0 accept comment "RULE 133 - HTTPS only ALLOW"
ip daddr { 10.3.0.10 } counter packets 0 bytes 0 drop comment "RULE 133 - HTTPS only DENY"
```

The **DENY** rule still matches all traffic to the destination regardless of port or protocol - it blocks everything not explicitly permitted by the **ALLOW** rule.

#### IP Forwarding

For traffic to flow between your network interfaces on Linux you may also need to enable IP forwarding, if you haven't done it already. This can be achieved by setting the following variable with the following commands:

```
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv6.conf.all.forwarding=1
```

If the change should be persistent, edit the `sysctl` configuration file `/etc/sysctl.conf` and add the following lines to it:

```
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
```

To load your changes from `sysctl.conf`, use `sysctl -p`.

#### Masquerade

Masquerading between network interfaces falls outside the scope of Defguard’s responsibilities and must be handled by the system administrator. If your environment doesn’t already provide proper routing between the gateway’s interfaces, you may need to enable masquerading to ensure seamless communication.

As a shortcut, Defguard Gateway offers the `--masquerade` flag (or the `DEFGUARD_MASQUERADE=true` environment variable), which applies source NAT between all interfaces automatically, saving you from manually configuring masquerade rules at the system level. It results in this masquerade NFTables rule:

```
    chain POSTROUTING {
            ...
            oifname != "lo" counter packets 4 bytes 240 masquerade
    }
```

{% hint style="warning" %}
The `--masquerade` option applies masquerading between **all** interfaces on the gateway, which may be more permissive than necessary in some environments. While convenient, this broad behaviour might not align with more restrictive or segmented network designs. For greater control and tighter security, we recommend that administrators configure masquerading manually between only the interfaces that require it.
{% endhint %}

#### Forward chain priority

Defguard creates a FORWARD chain in its own table to control which forwarded packets are allowed or blocked. This may interfere with your other NFTables rules if they also use a FORWARD chain.

```
chain FORWARD {
	type filter hook forward priority filter; policy deny;
	ct state established,related counter packets 119 bytes 13404 accept
}
```

By default the chain runs at priority `filter` (0). You can adjust this with the `DEFGUARD_FW_PRIORITY` environment variable (or `fw_priority` config option). The higher the value, the later the chain runs relative to your other forward chains. For example, setting `DEFGUARD_FW_PRIORITY=1` causes Defguard's chain to run after any chains at priority 0.

#### Disabling firewall management

If your hardware or environment is incompatible with Defguard's firewall management, you can disable it entirely with the `DEFGUARD_DISABLE_FW_MGMT=true` environment variable (or `--disable-firewall-management` flag). When set, the gateway will not create or modify any firewall rules, and all firewall configuration received from Core will be silently ignored. Use this only as a workaround - firewall rules will not be enforced in this mode.

### Packet Filter (FreeBSD, NetBSD, macOS)

{% hint style="info" %}
Defguard Gateway supports the Packet Filter (PF) firewall.
{% endhint %}

Packet filter (PF) firewall, is a BSD-licensed stateful packet filtering software originally developed for OpenBSD and now ported to other BSD-based systems like FreeBSD, NetBSD, and macOS.

#### Anchors

To avoid interference with other PF rules, Defguard uses a dedicated anchor per network interface. All rules for a given interface are stored under `defguard/<ifname>`.

To see all anchor created by Defguard Gateway, enter:

```
pfctl -a defguard -sA
```

To display all rules created for interface _wg0_, enter:

```
pfctl -a defguard/wg0 -sr
```

#### Enabling Defguard rules in PF

Currently, Defguard Gateway does not automatically include its anchor in the general PF rules. This has be done explicitly, using one of the following methods:

1. In PF configuration file _/etc/pf.conf_, which should include a line like the following (see pf.conf(5) manual page for details):

```
anchor "defguard/*" all
```

2. Manually in the terminal, but this will remove all existing top-level rules:

```
echo 'anchor "defguard/*" all' |pfctl -f -
```

{% hint style="info" %}
This is not required for OPNsense plug-in for Defguard Gateway as it automatically creates top-level PF rule to include the appropriate rules.
{% endhint %}

#### Example

Using the same _Staff access, Berlin_ rule from the NFTables example above, the PF rules for interface `wg0` would look similar to:

```
block drop in log on wg0 all flags S/SA keep state
pass in log quick on wg0 inet from 10.100.200.155 to 10.1.1.0/24 flags S/SA label "RULE 132 - Staff access, Berlin ALLOW"
pass in log quick on wg0 inet from 10.100.200.156 to 10.1.1.0/24 flags S/SA label "RULE 132 - Staff access, Berlin ALLOW"
block drop in log on wg0 inet from any to 10.1.1.0/24 flags S/SA label "RULE 132 - Staff access, Berlin DENY"
```

PF does not support address sets in the same way as NFTables, so each source IP is expressed as a separate rule. The **DENY** rule blocks all other traffic to the destination from any source.

### Merging destination IP addresses

Destinations in a firewall rule can be configured in multiple ways:

* Single IP address
* IP range
* IP subnet in CIDR notation
* Any combination of the above
* Destinations
* Aliases

It is therefore possible to configure overlapping destinations - for example, a `10.0.20.0/24` subnet and a specific IP `10.0.20.17` from an Alias used in the same rule.

When generating firewall rules, Defguard must account for the following platform constraints:

* `nft` rejects overlapping destination sets
* PF does not handle IP ranges natively, so each IP in a range requires a separate rule

To work around these constraints, destination addresses are pre-processed before being sent to the gateway:

1. All destinations (manually configured, aliases, ranges etc) are combined into a single list
2. All address types (single IPs, ranges, subnets) are converted to IP ranges.
3. Ranges are merged into the smallest possible list of non-overlapping ranges.
4. Subnets (containing at least 2 IPs) are extracted from ranges where possible.

This processing is biased towards subnet notation. As a result, the destinations you see in the actual firewall rules on the gateway may differ significantly in notation - though not in coverage - from what you configured in the UI. This is expected behaviour and can be observed by inspecting the rules directly using the commands described above.
