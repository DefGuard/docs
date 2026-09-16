# Gateway/Location VLAN routing

This guide will showcase how to segment a VPN Location and egrees the VPN traffic into local network on a selected VLAN.\
In this guide we are assume the following:

* Gateway is running on a Linux machine
* Users VPN IP needs to be visible in the internal network **as is** thus, we will not use **NAT** (as nat masks the user/device IP) and we will route VPN network into the internal network.
* In this example we will showcase three different VPN locations on the same host - which require three gateways running (on different public WireGuard ports and private gRPC ports).

#### Reference topology & assumptions

```
Location A  10.0.0.0/24  ─┐
Location B  10.0.1.0/24  ─┤   ┌────────────┐   eth0.10 ──► VLAN 10 ──► 192.168.10.0/24
Location C  10.0.2.0/24  ─┴─► │  WG host / │   eth0.20 ──► VLAN 20 ──► 192.168.20.0/24
           (wgX tunnel)       │  router    │   eth0.30 ──► VLAN 30 ──► 192.168.30.0/24
                              └────────────┘   (eth0 = 802.1Q trunk to switch)
```

Adjust names/ports/subnets to your environment: `eth0` is the trunk carrying VLANs 10/20/30 to the switch; the LAN segments are `192.168.{10,20,30}.0/24`. Keys below are placeholders.

***

### Step 1: Enable forwarding and loosen `rp_filter`

Forwarding is required. Because routing without NAT can create asymmetric paths, strict reverse-path filtering (the default on many systems) will silently drop return traffic — set it to _loose_ (mode 2).

```ini
# /etc/sysctl.d/99-vpn.conf
net.ipv4.ip_forward = 1
net.ipv4.conf.all.rp_filter = 2
net.ipv4.conf.default.rp_filter = 2
```

```bash
sudo sysctl --system
sysctl net.ipv4.conf.all.rp_filter      # expect 2
```

> **Why both `all` and `default`:** the kernel uses `max(all, per-interface)`, and VLAN interfaces created afterwards inherit the `default` value.

***

### Step 2: WireGuard Internal VPN Networks

In Defguard -> Locations -> _Location A_ make sure it has assigend the internal VPN network (see diagram above). This means that devices conneted to this network will have IPs assigned from this network:

<figure><img src="../.gitbook/assets/Screenshot 2026-06-24 at 11.51.34.png" alt=""><figcaption></figcaption></figure>

***

### Step 3: Create the VLAN interfaces

Since this host is the gateway for each segment, give it the `.1` address on every VLAN.

{% hint style="info" %}
We assume `eth0` as the main internal network egress interface and 192.168.x.0 network as he internal network assigned to the VLAN - change it acordingly.

You can have different/multiple egress interfaces, the configuration is up to you.
{% endhint %}

```bash
for v in 10 20 30; do
  ip link add link eth0 name eth0.$v type vlan id $v
  ip link set eth0.$v up
done

ip addr add 192.168.10.1/24 dev eth0.10
ip addr add 192.168.20.1/24 dev eth0.20
ip addr add 192.168.30.1/24 dev eth0.30
```

***

### Step 4: Policy-based routing by source subnet

Each VPN Location gets its own routing table and a rule that diverts source traffic into it. These `from` rules also apply to _forwarded_ packets, which is exactly the tunnel-to-LAN traffic we want to steer.

Pick **one** of the two options below depending on how far each Location should reach.

#### Option A: Location reaches its own segment only

The VPN Location can only talk to its matching LAN segment; everything else falls back to the `main` table.

```bash
# named tables
echo "110 vlan10" | sudo tee -a /etc/iproute2/rt_tables
echo "120 vlan20" | sudo tee -a /etc/iproute2/rt_tables
echo "130 vlan30" | sudo tee -a /etc/iproute2/rt_tables

# Location A -> VLAN 10
ip rule  add from 10.0.0.0/24 table vlan10
ip route add 192.168.10.0/24 dev eth0.10 table vlan10

# Location B -> VLAN 20
ip rule  add from 10.0.1.0/24 table vlan20
ip route add 192.168.20.0/24 dev eth0.20 table vlan20

# Location C -> VLAN 30
ip rule  add from 10.0.2.0/24 table vlan30
ip route add 192.168.30.0/24 dev eth0.30 table vlan30
```

#### Option B: Location's full default route via its VLAN

_All_ of the Location's egress leaves through its assigned VLAN (the 802.1Q tag and source address preserved), via the upstream gateway on that VLAN.

```bash
# named tables
echo "110 vlan10" | sudo tee -a /etc/iproute2/rt_tables
echo "120 vlan20" | sudo tee -a /etc/iproute2/rt_tables
echo "130 vlan30" | sudo tee -a /etc/iproute2/rt_tables

# Keep VPN-internal traffic (peer <-> peer, peer <-> host) on the main table.
# Higher priority (lower pref) than the per-Location rules, so a Location's default
# route does NOT swallow traffic aimed at the other pools.
ip rule add to 10.0.0.0/22 lookup main pref 1000

# Location A -> everything via VLAN 10
ip rule  add from 10.0.0.0/24 lookup vlan10 pref 1010
ip route add default via 192.168.10.254 dev eth0.10 table vlan10

# Location B -> everything via VLAN 20
ip rule  add from 10.0.1.0/24 lookup vlan20 pref 1011
ip route add default via 192.168.20.254 dev eth0.20 table vlan20

# Location C -> everything via VLAN 30
ip rule  add from 10.0.2.0/24 lookup vlan30 pref 1012
ip route add default via 192.168.30.254 dev eth0.30 table vlan30
```

Replace `192.168.x.254` with the real next-hop router on each VLAN.

Three things to mind with Option B:

1. **Real next-hop required.** `default via <gw>` only works if `<gw>` exists on that VLAN and forwards onward. If the WG host is the only device with nothing upstream, use Option A instead.
2. **Protect VPN-internal traffic.** With only a `default` in the table, the `from` rule would push _every_ packet out the VLAN — including peer-to-peer and to-host traffic. The `to 10.0.0.0/22 lookup main pref 1000` rule sends anything destined for the three pools back to `main` first (where `wg0` lives). Adjust the `/22` aggregate if your pools span a different range; add directly-connected segments here too if Locations must reach them locally.
3. **No-NAT honesty for a default route.** A default route means the destination can be _anything_, including the public internet — and the wider world has no route back to your private `10.0.x.x` pools. Pure routing (no NAT) works end-to-end only if every hop toward that default has a return route to the pools. For direct internet egress the edge device facing the internet **must** NAT (the WG host can still stay NAT-free; the masquerade just moves to that edge router).

***

### Step 5: Return path (the critical part with no NAT)

Because the source stays `10.0.x.x`, every destination host must be able to send replies back to the VPN pools. How you handle this depends on who the segment gateway is.

#### Case A: Defguard Gateway host is the segment gateway

If `192.168.10.1` (etc.) is this host's own VLAN address, nothing extra is needed: the kernel already knows `10.0.0.0/24 dev wg0` in the `main` table, so replies returning to the host route straight back into the tunnel. This is the setup configured in Steps 3–4.

#### Case B: a separate router/firewall is the main gateway

Add static routes **on that device**, pointing each pool back at this WG host's address on the matching VLAN:

```
10.0.0.0/24 via <Defguard_Gateway_host_IP_on_VLAN10>
10.0.1.0/24 via <Defguard_Gateway_host_IP_on_VLAN20>
10.0.2.0/24 via <Defguard_Gateway_host_IP_on_VLAN30>
```

> **Common failure:** without these return routes, traffic leaves the tunnel and reaches the target, but the reply is dropped — classic symptom is "ping fails even though tcpdump shows the packet arriving."

#### Where the access policy lives

Since the downstream firewall now sees the real `10.0.x.x` addresses with a specific 802.1Q tag, the "who-can-reach-what" policy belongs there — on the switch/firewall, keyed off the VLAN tag. The WG host just does clean L3 forwarding plus tagging.

***

### Step 6: Make it persistent

The commands above are runtime-only. For production on a systemd host, the cleanest option is `systemd-networkd`, which keeps the VLANs and PBR rules independent of the tunnel's lifecycle (they won't vanish when the tunnel restarts):

* a `.netdev` file per VLAN (kind `vlan`, with the VLAN id);
* a `.network` file per VLAN carrying the address, a `[Route]` with `Table=`, and a `[RoutingPolicyRule]` with the `From=` pool (and a second rule for the `to 10.0.0.0/22 lookup main` exception if you use Option B).

Alternatively, drop the `ip` commands into `PostUp`/`PostDown` in `wg0.conf` — simpler, but the rules are then tied to the tunnel restarting.

***

### Step 7: Verify

```bash
wg show
ip rule                          # from 10.0.x.0/24 → vlanXX (plus the to-main rule in Option B)
ip route show table vlan10       # Option A: 192.168.10.0/24 dev eth0.10
                                 # Option B: default via 192.168.10.254 dev eth0.10
```

Then prove the Location→VLAN mapping on the wire. From a Location A client, reach a host on the VLAN 10 path and watch both directions:

```bash
# is the reply coming back on the VLAN interface?
tcpdump -eni eth0.10 host 10.0.0.2

# confirm the 802.1Q tag + original (un-NATed) source on the trunk:
tcpdump -eni eth0 vlan 10 and src net 10.0.0.0/24
```

Seeing the `vlan 10` tag in the header alongside the original `10.0.0.x` source is the definitive proof that Location-to-VLAN mapping works. If `eth0.10` only shows outbound packets and no replies, the problem is the return path (Step 5), not the WG host.
