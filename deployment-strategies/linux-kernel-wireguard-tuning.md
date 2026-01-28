---
description: >-
  This technical guide addresses requirements regarding WireGuard VPN latency,
  throughput, resilience, and scalability by tuning Linux Kernel parameters (as
  WireGuard is part of Linux Kernel).
---

# Linux Kernel WireGuard tuning

## Introduction

WireGuard is widely praised for its lean codebase and efficiency. However, the default Linux kernel settings are often tuned for general-purpose computing, not for acting as a high-speed router handling encrypted UDP traffic at scale.

Here are some tuning parameters to achieve maximum performance (low latency), stability across changing networks (roaming), and high concurrency, we must tune three distinct layers.

{% hint style="info" %}
Kernel **sysctl** settings optimize how the Linux kernel schedules packets and manages memory buffers. Add the following to `/etc/sysctl.d/99-wireguard-tuning.conf`. or `/etc/sysctl.conf`
{% endhint %}

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

`net.core.netdev_budget` limits how many packets the kernel may process in a single SoftIRQ cycle before yielding the CPU and it's default is: 300 packets.

* Too low value - Under heavy load (e.g., 100+ streaming users), the kernel yields too early, packets back up in the NIC buffer, and drops occur.
* Too high value - The networking stack can monopolize a CPU core, starving userspace processes and increasing overall latency.

For high-performance VPN servers, we increase `netdev_budget` to favor network throughput and tune the companion setting `netdev_budget_usecs` to cap CPU time per polling cycle. Below you wil find some recommended values for multiple scenarios.

#### Home/Small Office

Meaning around \~20 users: The default 300 is fine and changing it won't be noticeable.

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

WireGuard is stateless, but the Linux firewall tracking (used for Masquerade or DNAT when configuring connection tracking and egress through VPN) is stateful.

Here is a formula how to optimize netfliter parameters based on the following assumptions that one connected device is a UDP stream (VPN) and multiple TCP streams that the user/device "uses" for browsing/apps "exiting" via VPN:

{% hint style="warning" %}
**Assumptation**:\
\
1 active user generates \~50-100 simultaneous connections
{% endhint %}

<table data-full-width="true"><thead><tr><th width="283.56640625">Parameter (Sysctl)</th><th width="181.859375">Description</th><th width="91.12109375">10 Devices(Home/SOHO)</th><th width="95.640625">100 Devices(SMB/Office)</th><th width="98.55859375">1,000 Devices(Enterprise/ISP)</th><th width="107.7265625">10,000 Devices(Data Center)</th></tr></thead><tbody><tr><td><code>net.netfilter.nf_conntrack_max</code></td><td>CRITICAL. Max concurrent connections tracked.</td><td>65536</td><td>131072</td><td>524288</td><td>5242880</td></tr><tr><td><code>net.core.somaxconn</code></td><td>Max pending connections in queue.</td><td>4096</td><td>4096</td><td>16384</td><td>65535</td></tr><tr><td><code>net.core.netdev_max_backlog</code></td><td>Max packets queued if kernel is busy.</td><td>1000</td><td>5000</td><td>16384</td><td>65535</td></tr><tr><td><code>net.core.netdev_budget</code></td><td>Max packets processed in one CPU cycle.</td><td>300</td><td>600</td><td>600</td><td>1200</td></tr><tr><td><code>net.core.rmem_max</code> (Bytes)</td><td>Max OS receive buffer size (UDP).</td><td>16 MB</td><td>16 MB</td><td>32 MB</td><td>128 MB</td></tr><tr><td><code>net.core.wmem_max</code> (Bytes)</td><td>Max OS send buffer size (UDP).</td><td>16 MB</td><td>16 MB</td><td>32 MB</td><td>128 MB</td></tr><tr><td><code>fs.file-max</code></td><td>System-wide file descriptor limit.</td><td>Default</td><td>100000</td><td>1000000</td><td>5000000</td></tr><tr><td><strong>Required System RAM</strong></td><td>Minimum RAM needed for state tables.</td><td>512 MB</td><td>1 GB</td><td>4 GB</td><td>32 GB+</td></tr></tbody></table>

