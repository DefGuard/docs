# Interface metrics on Windows

On Windows, when Defguard Client is configured to route **all traffic** through the VPN, the tunnel
interface’s metric is set to **0**. For **predefined traffic**, the tunnel interface’s metric is set
to **automatic**.

Windows uses the interface metric to choose between routes of equal specificity and to order DNS
servers across adapters. A lower value means a higher priority, so 0 is the highest possible
priority.

In an **all traffic** setup, this makes the VPN’s default route take precedence over the physical
adapter’s default route, and makes Windows send DNS queries to the tunnel’s DNS servers first. This
helps prevent traffic and DNS leaks outside the tunnel.

In a **predefined traffic** (split-tunnel) setup, the metric is left at its default, automatic
value. Forcing it to 0 would give the tunnel’s DNS servers priority for every lookup, including
local and non-tunneled names, which could break local name resolution or send queries through the
VPN unnecessarily.

To check the tunnel interface metrics, run the following commands for IPv4 and IPv6 respectively:

```
netsh interface ipv4 show interfaces
netsh interface ipv6 show interfaces
```
