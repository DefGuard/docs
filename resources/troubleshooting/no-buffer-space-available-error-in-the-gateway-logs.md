# "No buffer space available" error in the gateway logs

## Problem

The following error appears in the gateway logs:

```bash
Interface configuration failed: Firewall error: Netlink error: Failed while reading a message from socket: Os { code: 105, kind: Uncategorized, message: "No buffer space available" }
```

## Symptoms

* This error appears in the logs.
* VPN functionality continues to work.
* No stats are visible on the dashboard.

## Cause

This issue is related to **insufficient socket buffer sizes** on the host system, which causes the Netlink communication with the Linux kernel to fail.

## Solution

To resolve the issue, increase the system's socket buffer sizes by updating your `sysctl.conf` file.

#### 1. Edit `/etc/sysctl.conf`

Add the following lines to the end of your file:

```bash
bashCopyEdit# Increase socket buffer sizes
net.core.rmem_max = 67108864
net.core.wmem_max = 67108864
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_wmem = 4096 65536 67108864
net.core.rmem_default = 524288
net.core.wmem_default = 524288
```

#### 2. Apply the changes

Run the following command to reload the settings:

```bash
sudo sysctl -p
```

## Verification

* Check the gateway logs to ensure the error no longer appears.
* Confirm that stats are now visible in the dashboard.
