---
description: 'VPN Speed Testing Guide: Linux Server & Windows/Linux Clients'
---

# Testing VPN speed

This guide explains how to measure the raw network throughput of your VPN network using a dedicated tool called: **iPerf3**.\
VPN network speed needs to be mesured between a selected VPN client and a server running internal network (behind a VPN).

Comparing your baseline speed to a VPN connection using services like SpeedTest.net is a flawed apples-to-oranges comparison because it measures entirely different traffic routes to external testing servers that Speedtest randomly selects for each run, completely failing to isolate the true throughput of your private internal tunnel.

**Also - unlike copying files, iPerf3 tests pure network performance without being limited by slow hard drives or protocol overhead.**

{% hint style="warning" %}
Select a central server in your network to host the iPerf3 server. For the most accurate results, we recommend testing from two or three different clients, ideally using different Internet Service Providers (ISPs) and connection types (e.g., Fiber, LTE/5G, or Wi-Fi).

Important: Ensure that clients run their tests sequentially (one after another) rather than simultaneously, so they do not compete for the server's bandwidth and skew the data.
{% endhint %}

{% hint style="success" %}
**We highly reccomend** before doing the testing [to read and implement WireGuard tuning described in this article](../../deployment-strategies/linux-kernel-wireguard-tuning.md).
{% endhint %}

### 1. Setup the Linux Server

The server will sit and listen for incoming test connections from your clients.

#### Step A: Install iPerf3

Log into your Linux server and run the appropriate command for your distribution:

* **Ubuntu / Debian / Mint:**

```bash
sudo apt update && sudo apt install iperf3 -y
```

* **CentOS / RHEL / Rocky Linux:**

```bash
sudo dnf install iperf3 -y
```

#### Step B: Open the Firewall

iPerf3 uses port **5201** by default. You must allow traffic through this port:

* **If using UFW (Ubuntu default):**

```bash
sudo ufw allow 5201/tcp
```

* **If using firewalld (RHEL/CentOS default):**

```bash
sudo firewall-cmd --add-port=5201/tcp --permanent
sudo firewall-cmd --reload
```

#### Step C: Start the Server

Run iPerf3 in server mode:

```bash
iperf3 -s
```

_Leave this terminal window open. It is now waiting for clients._

***

### 2. Test from a Windows Client

#### Step A: Install iPerf3

Open **PowerShell** or **Command Prompt** and run:

```shellscript
winget install iperf3
```

_(Note: After the installation finishes, restart your PowerShell/CMD window so the system recognizes the new command)._

#### Step B: Run the Test

Run the following command, replacing `SERVER_IP` with your actual Linux server IP address (e.g., `192.168.1.50`) **accessible from the VPN network:**

```cmd
iperf3 -c SERVER_IP
```

***

### 3. Test from a Linux Client

#### Step A: Install iPerf3

Open the terminal on your client Linux machine and install the tool:

```bash
sudo apt install iperf3 -y  # For Ubuntu/Debian
# OR
sudo dnf install iperf3 -y  # For RHEL/CentOS
```

#### Step B: Run the Test

Run the following command, replacing `SERVER_IP` with your actual Linux server IP address (e.g., `192.168.1.50`) **accessible from the VPN network:**

```bash
iperf3 -c SERVER_IP
```

***

### 4. Understanding the Results (WireGuard VPN Context)

Since you are testing a **WireGuard VPN** connection, performance is heavily limited by:

1. **The Server's Upload Speed:** If your server has a 100 Mbps upload limit, clients will never download faster than 100 Mbps, regardless of their own internet speed.
2. **CPU Encryption Limits:** WireGuard is extremely fast, but weak CPUs (like cheap routers or old servers) will bottleneck the throughput.
3. **Protocol Overhead:** Encryption and MTU encapsulation add roughly 5-10% performance tax.

#### Realistic VPN Speed Expectations

Here is a breakdown of realistic iPerf3 results based on the client's underlying connection type (assuming the server side has sufficient upload bandwidth):

| Client Connection Type                                                | Expected iPerf3 Result | Real-World Context & Bottlenecks                                                                                                      |
| --------------------------------------------------------------------- | ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>Fiber</strong><br>via <em>Ethernet Cable</em></p>          | `200 - 800 Mbits/sec`  | **Excellent.** This is the practical limit for most modern consumer hardware handling WireGuard encryption over the public internet.  |
| <p><strong>Wi-Fi 6 / 5</strong><br><em>on a Fiber connection</em></p> | `100 - 450 Mbits/sec`  | **Good.** Wi-Fi adds minor latency fluctuations (jitter), which slightly degrades VPN performance compared to a physical cable.       |
| <p><strong>5G Mobile Data</strong><br><em>Good signal</em></p>        | `50 - 250 Mbits/sec`   | **Variable.** Modern 5G networks handle VPNs very well, but speeds will fluctuate depending on your distance from the cellular tower. |
| **LTE (4G) Mobile Data**                                              | `15 - 60 Mbits/sec`    | **Highly Limited.** LTE suffers from higher latency and bufferbloat, which aggressively chokes VPN throughput.                        |

#### ⚠ The Golden Rule of VPN Testing

If a client gets **80 Mbits/sec** over LTE, but **400 Mbits/sec** over Fiber, the VPN server is working perfectly. The bottleneck is entirely the user's current internet connection, not your WireGuard setup.

***

### 💡 Advanced Tips

* **Test Upload vs. Download:** By default, the client _sends_ data (Upload). To test the reverse direction (Download from server to client), add the `-R` flag:

```bash
iperf3 -c SERVER_IP -R
```

* **Change Test Duration:** To run the test for 30 seconds instead of 10, add `-t 30`:

```bash
iperf3 -c SERVER_IP -t 30
```

* **Stress Test (Parallel Streams):** To push your network to its absolute limit using multiple simultaneous connections, add `-P 4`:

```bash
iperf3 -c SERVER_IP -P 4
```
