---
icon: user-shield
---

# Access Control List: Implementation Details

{% hint style="info" %}
See Examples section in [Access Control List](README.md) documentation as they relate to details described below.
{% endhint %}

## Firewall Interaction

Defguard Gateway does **not** take control of the entire firewall. Instead, dedicated chains (in NFTables) and anchors (in PF) are used as not to interfere with other rules on the firewall.

## NFTables (Linux)

All applied rules are deployed to Defguard Gateway. This means that the firewall on the Gateway that handles the _Office-Berlin_ location should contain appropriate [NFTables](https://nftables.org/) rules that implement the specified requirements. Let's see how this looks like in practice. Typing `nftables list ruleset` in the Terminal on a machine running Defguard Gateway should display something like the following:

```
...
table inet DEFGUARD {
        chain FORWARD {
                type filter hook forward priority filter; policy drop;
                ct state established,related counter packets 0 bytes 0 accept
                ip saddr { 10.100.200.155-10.100.200.156 } ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 accept comment "ACL 132 - Staff access Berlin ALLOW"
                ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 drop comment "ACL 132 - Staff access Berlin DENY"
        }
}
...
```

As you can see, Defguard has created a new table of type _inet_ (the one that handles both IP v4 and v6 addresses). This is to make sure Defguard's configuration won't interfere with your existing NFTables entries.

The FORWARD chain specifies our rules. First you can see the `policy drop` default, which is a result of setting the **Default Deny** policy in the location settings. Then the `established,related` line to skip re-evaluation of established connections.

Finally the two lines that directly deal with our requirement to allow the two users into the network.

```
ip saddr { 10.100.200.155-10.100.200.156 } ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 accept comment "ACL 132 - Staff access Berlin ALLOW"
```

This rule specifies two addresses as the **Traffic Source –** `10.100.200.155` and `10.100.200.156`. Those happen to be device addresses of our two users in the WireGuard VPN network that Defguard Gateway manages for this location. The destination address `10.1.1.0/24` is exactly the network address we specified in the rule. And finally the `accept` verdict. All together, this rule allows the traffic specified in the UI to the network.

You may also notice that Defguard added a comment to the rule. The comment includes rule's name so that it is easy for you to find the corresponding rule using tools like `grep`.

Finally, the last line:

```
ip daddr { 10.1.1.0/24 } counter packets 0 bytes 0 drop comment "ACL 132 - Staff access Berlin DENY"
```

This line effectively blocks all other traffic to the 10.1.1.0/24 network. As mentioned earlier, the ACL rules in Defguard are self-contained and fully define access for their target resource. This set of rules can now be deployed to any gateway, no regardless of the **Default Policy** setting, and they will effectively do the same thing.

## Gateway deployment with ACL

Under the hood, Access Control functionality uses [NFTables](https://wiki.nftables.org/wiki-nftables/index.php/What_is_nftables%3F) to interact with the firewall and implement the rules. This means you'll need kernel version ≥ 5.10 to enable all kernel features required for proper operation.

### IP Forwarding

For traffic to flow between your network interfaces on Linux you may also need to enable IP forwarding, if you haven't done it already. This can be achieved by setting the following variable with the following commands:

```
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv6.conf.all.forwarding=1
```

If the change should be persistent, edit the sysctl configuration file `/etc/sysctl.conf` and add the following lines to it:

```
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
```

To load your changes from `sysctl.conf`, use `sysctl -p`.

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

## Packet Filter (FreeBSD, NetBSD, macOS)

Packet filter (PF) firewall, is a BSD-licensed stateful packet filtering software originally developed for OpenBSD and now ported to other BSD-based systems like FreeBSD, NetBSD, and macOS.

{% hint style="info" %}
Defguard Gateway supports Packet Filter (PF) firewall since version 1.4.0.
{% endhint %}

### Anchors

To avoid interference, Defguard Gateway uses custom anchors to add firewall rules. All rules controlled by Defguard are stored in _defguard_ anchor, and inside it, there are anchors dedicated to particular network interfaces.

To see all anchor created by Defguard Gateway, enter:

```
pfctl -a defguard -sA
```

To display all rules created for interface _wg0_, enter:

```
pfctl -a defguard/wg0 -sr
```

#### How to enable Defguard rules in PF

Currently, Defguard Gateway does not include its anchor in the general PF rules. This has be done explicitly, using one of the following methods:

1. In PF configuration file _/etc/pf.conf_, which should include a line like the following (see pf.conf(5) manual page for details):

```
anchor "defguard/*" all
```

2. Manually in Terminal, but this will remove all existing top-level rules:

```
echo 'anchor "defguard/*" all' |pfctl -f -
```

{% hint style="info" %}
This is not required for OPNsense plug-in for Defguard Gateway as it automatically creates top-level PF rule to include the appropriate rules.
{% endhint %}

### Rules

To display PF rules for network interface _wg0_, type the following command:

```
pfctl -a defguard/wg0 -sr
```

Rules create in _Office-Berlin_ example should correspond to something like:

```
block drop in log on wg0 all flags S/SA keep state
pass in log quick on wg0 inet from 10.100.200.155 to 10.1.1.0/24 flags S/SA label "ACL 132 - Staff access Berlin DENY"
pass in log quick on wg0 inet from 10.100.200.156 to 10.1.1.0/24 flags S/SA label "ACL 132 - Staff access Berlin DENY"
```
