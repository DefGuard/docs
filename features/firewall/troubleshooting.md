# Troubleshooting

### Rule is pending but not deployed

**Symptom:** A rule was created or modified but has no effect on traffic.

**Cause:** Changes to rules are staged as Pending Changes and do not take effect on gateways until explicitly deployed.

**Fix:** Go to the **Rules** page and click **Deploy pending changes**. Check that the rule moves from the **Pending Changes** section to the **Deployed Rules** section after deploying.

***

### Rule is deployed but the user is still blocked

**Symptom:** A firewall rule permits a user to access a resource, the rule is deployed, but the user still cannot reach it.

Before investigating the rule, determine whether the user can connect to the VPN location at all:

* If the user **cannot establish a VPN connection** to the location, the problem is not the firewall rule. Access to a VPN location is controlled by the location's **Allowed Groups** setting, which is independent of firewall rules. A user who is not in an allowed group has no WireGuard configuration for that location and cannot connect regardless of any rules. Check the location's Allowed Groups configuration and ensure the user is a member of one of the permitted groups.
* If the user **can connect to VPN** but still cannot reach the specific resource, the rule may not be correctly configured. Verify that:
  * The rule's destination matches the resource's IP address and the ports and protocols the user is trying to use.
  * The user, or a group they belong to, appears in the rule's Permissions.
  * The user is not being removed from the permitted set by a Restriction.
  * The rule is assigned to the correct location.

***

### Rule is deployed but not yet active on the gateway

**Symptom:** A rule is in the Deployed Rules section in Core but is not reflected in the gateway's firewall.

**Cause:** Core pushes firewall configuration to gateways over the management connection. If that connection was unavailable at the time of deployment, the update was not delivered. Defguard guarantees eventual consistency - the configuration will be pushed the next time the connection is established.

**Diagnosis:** If your Gateway has the optional health endpoint configured, check its status:

```
curl http://<gateway-address>:<health-port>/health
```

* `200 OK` - Gateway is connected to Core. If the rule is still not active, redeploy the pending changes.
* `503 Service Unavailable` - Gateway is not connected to Core. Investigate the network path between Core and the Gateway's management port (TCP 50066). Once connectivity is restored, Core will automatically push the current firewall configuration on reconnect.

***

### Disabling a rule has no immediate effect

**Symptom:** A rule was disabled in the UI but traffic it was controlling is still being permitted or blocked on the gateway.

**Cause:** Disabling a deployed rule creates a **pending change**, just like any other edit. The rule continues to be enforced on the gateway until the pending change is deployed.

**Fix:** Go to the **Rules** page, confirm the disabled rule appears in **Pending Changes**, and click **Deploy pending changes**.

***

### Rule looks correct in the UI but produces unexpected firewall behaviour

**Symptom:** A rule is configured and deployed correctly, but traffic is permitted or blocked in ways that don't match what the UI shows.

**Cause:** When generating firewall rules, Defguard processes and merges all destination addresses across all rules before sending them to the gateway. This can produce rules with different IP notation than what was configured - for example, an IP range entered in the UI may appear as one or more subnets in the actual firewall rules. The logical coverage is identical, but the representation differs.

Additionally, if multiple rules cover overlapping destinations, their combined firewall rules may interact in ways that are not immediately obvious from the UI.

**Diagnosis:** Inspect the firewall rules applied on the gateway directly. See Implementation Details for the exact commands to list active rules for NFTables and Packet Filter, and for an explanation of how destination addresses are merged and represented.

***

### Connection issues with Docker installed

If you have Docker installed on the same machine as your Gateway (or if you deployed Defguard using the one-line installation script) the firewall may not work properly due to Docker firewall rules interfering with your ACL rules.

To verify this, execute `nft list ruleset` (may require sudo) on the machine on which your Gateway is installed:

```
table ip filter {
	[...]
	chain FORWARD {
		type filter hook forward priority filter; policy drop;
		counter packets 744194 bytes 1580784676 jump DOCKER-USER
		counter packets 744194 bytes 1580784676 jump DOCKER-FORWARD
	}
	[...]
}
```

If you have a FORWARD chain (managed by Docker) that has a default policy of drop and filter priority, this chain will interfere with Defguard rules (accept policy shouldn't be a problem).

To fix this, set the [Defguard firewall priority](firewall-internals.md#forward-chain-priority) to -1. This will make Defguard rules run before Docker rules.

Running `nft list ruleset` on the machine should then result in the following:

```
table ip filter {
	[...]
	chain FORWARD {
		type filter hook forward priority filter; policy drop;
		counter packets 744194 bytes 1580784676 jump DOCKER-USER
		counter packets 744194 bytes 1580784676 jump DOCKER-FORWARD
	}
	[...]
}
table inet DEFGUARD-wg0 {
	chain FORWARD {
		type filter hook forward priority filter - 1; policy accept;
		iifname != "wg0" counter packets 13807 bytes 11743290 accept comment "Ignore traffic not related to the VPN"
		ct state established,related counter packets 2692 bytes 601240 accept comment "Allow established and related traffic
	}
	[...]
}
```

The Default forward chain that is managed by Docker has the priority of `filter` and Defguard chain of `filter - 1`, making it run before Docker's.
