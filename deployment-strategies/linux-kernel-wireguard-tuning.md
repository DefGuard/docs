---
description: >-
  This technical guide addresses WireGuard VPN latency, throughput, resilience,
  and scalability requirements by tuning Linux kernel parameters (as WireGuard
  is part of the Linux kernel).
---

# Linux Kernel WireGuard tuning

## Introduction

WireGuard is widely praised for its lean codebase and efficiency. However, the default Linux kernel settings are often tuned for general-purpose computing, not for acting as a high-speed router handling encrypted UDP traffic at scale.

To achieve maximum performance (low latency), stability across changing networks (roaming), and high concurrency, you should tune three distinct layers.

{% hint style="info" %}
Kernel **sysctl** settings optimize how the Linux kernel schedules packets and manages memory buffers. Add the following to `/etc/sysctl.d/99-wireguard-tuning.conf` or `/etc/sysctl.conf`.
{% endhint %}

## Hardware requirements

Before Linux Kernel tuning, please not that the hardware itself needs to be efficient in order to handle the bandwidth and user load.

Here are some general tips on hardware.

#### CPU Sizing Methodology for Defguard Gateways

Since WireGuard performs cryptographic operations (ChaCha20-Poly1305) directly inside the Linux kernel, CPU utilization scales primarily with **Aggregate Peak Throughput** and **Packets Per Second (PPS)**, rather than the raw number of idle connected peers.

**Core Formula:**

Required Cores (vCPUs) = Ceil( Expected Peak Throughput (Gbps) / Core Crypto Capacity (Gbps) ) + Dedicated Network Cores

Where:

1. Core Crypto Capacity: A modern server CPU core (with AVX2 or AVX-512 extensions) can process roughly 1.5 to 2.0 Gbps of encrypted WireGuard traffic (assuming an average mixed packet size of \~1000 bytes).
2. Expected Peak Throughput: Calculated based on the concurrency model: (Concurrent Active Users \* Average Peak Bandwidth per User).
3. Dedicated Network Cores (Overhead): Cores heavily consumed by SoftIRQs, NAPI polling (driven by netdev\_budget), and netfilter/conntrack processing.
   * For < 100 devices: 0 extra cores needed (handled by the base 2 cores).
   * For 1,000 devices: +2 cores should be allocated/optimized for network interrupt handling.
   * For 10,000 devices: +4 to +8 cores must be dedicated to RPS (Receive Packet Steering) to distribute the massive PPS load across the CPU complex.

#### RAM Memory

To estimate the system memory required by the kernel network stack and gateway components, use the following formula:

$$RAM_{total} = RAM_{base} + (N_{active} \times 0.003)$$

Where:

* RAM\_{total} is the total recommended system memory in Gigabytes (GB).
* RAM\_{base} is **1.0 GB** (the baseline allocation for a minimal Linux OS, the Defguard core/gateway agent, and basic userspace management processes).
* N\_{active} is the number of concurrent, actively transmitting devices.
* **0.003** (3 MB) is the memory overhead multiplier per active user under maximum burst conditions.

> #### 💡 Why 3 MB per user?
>
> This multiplier accounts for the optimized kernel configurations recommended in the tuning guide:
>
> 1. **Conntrack Entries:** An average active user spawns \~100 stateful connections. At \~320 bytes per `nf_conntrack` entry, plus hashing overhead, this consumes significant kernel memory.
> 2. **UDP Buffers:** High-throughput tuning relies on massive network windows (`net.core.rmem_max = 16MB` or higher). When multiple users blast traffic simultaneously, the kernel allocates large socket buffers (`sk_buff`) to prevent packet drops during CPU scheduling delays.

## Kernel tuning

### Congestion Control & Queuing (Latency & Throughput)

To reduce bufferbloat (latency spikes under load) and maximize throughput, we replace the default CUBIC algorithm with BBR (Bottleneck Bandwidth and Round-trip propagation time), which is less sensitive to packet loss and more aggressively seeks the optimal congestion window.

```ini
# Use BBR congestion control
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr
```

### Memory & Buffers (Throughput)

WireGuard uses UDP for data transport. By default, Linux kernel UDP buffer sizes are often too small for high-speed transfers (1 Gbps+), causing packets to be dropped in the kernel before WireGuard can process them.

```ini
# Increase default and max receive/send window sizes (approx 16MB)
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.rmem_default = 262144
net.core.wmem_default = 262144
net.ipv4.udp_mem = 4096 87380 16777216
```

### Packet Processing & Forwarding (Efficiency)

These settings allow the kernel to process packets faster and handle bursts of traffic without dropping them.

```ini
# Enable IP Forwarding (Required for VPN)
net.ipv4.ip_forward = 1

# Increase the maximum length of the processor input queue
# (Prevents drops during traffic bursts)
net.core.netdev_max_backlog = 5000

# Increase the maximum number of connections waiting for acceptance
net.core.somaxconn = 8192
```

### Packet buffering

In Linux, network cards (NICs) use **NAPI** (New API) polling to handle incoming packets. When an interrupt fires, the kernel disables further interrupts and polls the NIC, processing packets in batches.

`net.core.netdev_budget` limits how many packets the kernel may process in a single SoftIRQ cycle before yielding the CPU, and its default value is 300 packets.

* Too low a value - Under heavy load (e.g., 100+ streaming users), the kernel yields too early, packets back up in the NIC buffer, and drops occur.
* Too high a value - The networking stack can monopolize a CPU core, starving userspace processes and increasing overall latency.

For high-performance VPN servers, we increase `netdev_budget` to favor network throughput and tune the companion setting `netdev_budget_usecs` to cap CPU time per polling cycle. Below you will find some recommended values for multiple scenarios.

#### Home/Small Office

Meaning around \~20 users: the default value of 300 is fine, and changing it will likely not be noticeable.

### 50 VPN users and above

```
# --- NAPI Polling Budget Tuning ---

# Increase packet budget (Default: 300).
# Allow the CPU to process up to 600 packets in one cycle.
# Beneficial for high PPS (packets per second) environments.
net.core.netdev_budget = 600

# Increase the time budget (Default: 2000us or 2ms).
# Allow the NAPI cycle to run for up to 4ms before yielding.
# Prevents the loop from aborting prematurely during heavy traffic bursts.
net.core.netdev_budget_usecs = 4000
```

#### High throughput ≥ 10Gbps

You may need values as high as `netdev_budget = 1200`, assuming you have a powerful CPU with Receive Packet Steering (RPS) enabled.

## Multiple connection concurrency (egress via VPN)

WireGuard is stateless, but Linux connection tracking in the firewall, used for masquerading or DNAT when configuring egress through the VPN, is stateful.

Here is a way to optimize netfilter parameters based on the following assumption: one connected device generates one UDP stream for the VPN and multiple TCP streams used by the user or device for browsing and applications exiting through the VPN.

{% hint style="warning" %}
**Assumption**:\
\
1 active user generates \~50-100 simultaneous connections
{% endhint %}

<table data-full-width="true"><thead><tr><th width="283.56640625">Parameter (Sysctl)</th><th width="181.859375">Description</th><th width="91.12109375">10 Devices(Home/SOHO)</th><th width="95.640625">100 Devices(SMB/Office)</th><th width="98.55859375">1,000 Devices(Enterprise/ISP)</th><th width="107.7265625">10,000 Devices(Data Center)</th></tr></thead><tbody><tr><td><code>net.netfilter.nf_conntrack_max</code></td><td>CRITICAL. Max concurrent connections tracked.</td><td>65536</td><td>131072</td><td>524288</td><td>5242880</td></tr><tr><td><code>net.core.somaxconn</code></td><td>Max pending connections in queue.</td><td>4096</td><td>4096</td><td>16384</td><td>65535</td></tr><tr><td><code>net.core.netdev_max_backlog</code></td><td>Max packets queued if kernel is busy.</td><td>1000</td><td>5000</td><td>16384</td><td>65535</td></tr><tr><td><code>net.core.netdev_budget</code></td><td>Max packets processed in one CPU cycle.</td><td>300</td><td>600</td><td>600</td><td>1200</td></tr><tr><td><code>net.core.rmem_max</code> (Bytes)</td><td>Max OS receive buffer size (UDP).</td><td>16 MB</td><td>16 MB</td><td>32 MB</td><td>128 MB</td></tr><tr><td><code>net.core.wmem_max</code> (Bytes)</td><td>Max OS send buffer size (UDP).</td><td>16 MB</td><td>16 MB</td><td>32 MB</td><td>128 MB</td></tr><tr><td><code>fs.file-max</code></td><td>System-wide file descriptor limit.</td><td>Default</td><td>100000</td><td>1000000</td><td>5000000</td></tr><tr><td><strong>Required System RAM</strong></td><td>Minimum RAM needed for state tables.</td><td>512 MB</td><td>1 GB</td><td>4 GB</td><td>32 GB+</td></tr></tbody></table>
