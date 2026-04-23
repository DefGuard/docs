---
description: Here are some common issues / problems that are frequently encountered.
hidden: true
metaLinks:
  alternates:
    - https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/support-1/troubleshooting
---

# Troubleshooting Guide V1 legacy backup

{% hint style="info" %}
Before contacting support, please see if the answer cannot be found here:
{% endhint %}

## Desktop client real-time/auto sync doesn't work

The client communicates with core to initiate the handshake through the secure proxy - which is also the enrollment service - [more details about the architecture here](../../in-depth/architecture/architecture.md) so it's critical it works and is properly configured.

Common problems may be:

1. proxy / enrollment isn't working
2. DEFGUARD\_ENROLLMENT\_URL is set incorrectly in core - if set incorrectly the client will try to connect to default URL which is "http://localhost:8080" and MFA will not work.

## Nothing happens after clicking on "Use authenticator app" in client

See the "[Desktop client real-time/auto sync doesn't work](./#desktop-client-real-time-auto-sync-doesnt-work)" answer.

## I use Multi-Factor Authentication and am disconnected after _X-time_

location, After enabling Multi-Factor Authentication for a location, the configuration of the gateway changes. Without MFA, peers (devices) are persistent (always in the Kernel memory) and gateway only changed the gateway configuration if a peer is added/removed/changed.

But when MFA is enabled, peers **are only added to the gateway** after successful MFA on the client (and pre-shared key exchange with client and gateway to establish a dedicated key for the session).

Also, there is a setting in the location named _Peer Disconnect Threshold (seconds):_

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-15 at 18.25.33.png" alt="" width="375"><figcaption></figcaption></figure>

This setting specifies that if the **peer is inactive for&#x20;**_**(defined seconds)**_, the gateway **should remove it from the configuration** (as it should not be persistent since MFA is required).

So if you are disconnected from the location:

1. Check what is your setting for peer disconnect.
2. We have a known bug - that after disconnecting Defguard Desktop Client doesn't properly detect that and has a still active connection (and actually is disconnected). You need to reconnect. Also, it's [being fixed and will be released in 1.1 version of the client](https://github.com/DefGuard/client/issues/351).

## Client: failed to configure DNS (Linux)

This error commonly occurs on Ubuntu 22. Defguard client internally calls `resolvconf` to set DNS servers. The only tested backend is `systemd-resolved`, so make sure you use it before proceeding further (`systemctl status systemd-resolved`).

On newer Ubuntu distributions (23 and up) `resolvconf` is, by default, a symbolic link to `resolvectl` and this is the recommended way of interacting with the system's DNS configuration. On Ubuntu 22 the symbolic link doesn't exist and the most straightforward way to fix this issue is to manually create it:

```bash
sudo ln -s /usr/bin/resolvectl /usr/sbin/resolvconf
```

If this fails, one may also try installing one of the packages providing the `resolvconf` command, like `openresolv` or, ⁣ but`resolvconf` this has not been tested and may cause issues with `systemd-resolved`, so proceed at your own risk.

### Check DNS name resolution

If the internal DNS servers are configured but users can't connect to internal hosts requiring name resolution, you may want to check the following:

1. Routing: `ip route` - are requests to network segments where DNS servers live routed through the wireguard interface?
2. Wireguard interface: `sudo wg` - are network segments where DNS servers live on the `allowed ips` list?
3. Try resolving the names manually using your DNS servers: `dig @DNS_SERVER_IP my.internal.service.com`

## Client: Failed to parse IP address

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

This error usually indicates that the client version is outdated and can't communicate with proxy/core services. Try upgrading the desktop client application.

## Client: **Could not start MFA process**

When setting up MFA/2FA in Defguard, you may encounter the following error when entering the code in the desktop client: **"Could not start MFA process".**

Additionally, client application debug logs may show the following error:\
\&#xNAN;**"Failed to deserialize the JSON body into the target type: code: invalid type: string "005325", expected u32 at line 1 column 268."**

This error indicates that one of the following components is outdated:

* Defguard core
* Defguard proxy
* Client application

Try upgrading these components to the latest stable version to resolve the issue.

## TOTP / Email codes for MFA do not work

If you are having problems with TOTP codes form 2FA/MFA (when logging in to Defguard or when connecting to VPN) please make sure your clock on the server that Defguard core is running is set properly.

Best would be to set up on the server NTP time synchronization.

## I get the following error: There was a network error. Can't reach proxy

This error may happen when the client is unable to make a POST/GET request to your proxy web server. Make sure your proxy is accessible by the client, e.g. by making a GET request by hand or visiting your enrollment/proxy page from the same machine you are running the client on.

In some rarer cases, your web server may not accept TLS versions lower than 1.3. Since the maximum version of TLS supported by the client is 1.2, it won't be able to connect to your web server, producing the mentioned error.

## I get the following error: _h2 protocol error: http2 error: stream error received: not a result of an error_

This error is common if you use a reverse-proxy for any of our components.

Every reverse proxy has a timeout for keeping the connection alive. You can increase the timeout value to see less errors, but they will eventually appear.

Ignore them, this is a normal behaviour.

## Gateway throws "No buffer space available" error

In the log files, you can see the following message:

```
Jul 08 08:33:24 defguard defguard-gateway[1241]: [2025-07-08T06:33:24Z ERROR defguard_gateway::gateway] Failed to update network configuration: Firewall error: Netlink error: Failed while reading a message from socket: Os { code: 105, kind: Uncategorized, message: "No buffer space available" }
```

To fix this, you'll need to set larger socket buffer sizes using kernel parameters:

```
net.core.rmem_max = 67108864
net.core.wmem_max = 67108864
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_wmem = 4096 65536 67108864
net.core.rmem_default = 524288
net.core.wmem_default = 524288
```

More info here: [https://github.com/DefGuard/defguard/issues/1303](https://github.com/DefGuard/defguard/issues/1303)

## Enrollment URL shows http://localhost:8080 despite `DEFGUARD_URL` is different

You are probably looking for `DEFGUARD_ENROLLMENT_URL` which is the URL needed to add device (image below).

<figure><img src="../../.gitbook/assets/DEFGUARD_ENROLLMENT_URL (1).png" alt=""><figcaption></figcaption></figure>

Please check [this article](../../deployment-strategies/configuration.md#enrollment-configuration).

## Enrollment URL has changed

If for any reason the Enrollment URL has changed (eg. the domain from: _enroll.company.com_ to _setup.company.com),_ all desktop or mobile aplications needs to be manually updated with the new URL.

{% hint style="warning" %}
**This manual process including tokens issued by administrator is intentional** – so that neither the user themselves, nor a potential attacker, can manually change the configuration or do so without the user/administrator’s knowledge.
{% endhint %}

In order to do so:

* **The administrator** needs to send an update token to each person - [see this documentation page.](https://docs.defguard.net/features/remote-user-enrollment#restarting-enrollment-manually)
* **The user** needs to update manually the Enrollment URL providing also the received token - [see this documentation page](../../using-defguard-for-end-users/desktop-client/).

## How to check if the VPN is working / my VPN is not working

If you have configured your Defguard instance correctly, after connecting to the VPN you should be able to ping your VPN server, for example if you have the following setup:

<figure><img src="../../.gitbook/assets/Screenshot 2024-03-24 at 18.36.43.png" alt="" width="313"><figcaption><p>Example VPN server IP</p></figcaption></figure>

After connecting to VPN you should be able to ping: 10.1.1.1.

### VPN Location settings changed

Check if the VPN location configuration has changed. If it did, and you do not have Enterprise license where real-time config sync is automatic, the user needs to [update their client configuration by updating that instance manually.](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#updating-instance)

### Conflicting networks

If you are **not able to ping the VPN server,** the **most common problem is that you have chosen a network that may be in conflict with your other networks** (local network, network from your Internet provider, etc.).

To examine your routing use on Mac and Linux `netstat -rn` command. Let's look at the example from above, the VPN network is: 10.1.1.0/24, let's look at the network route table:

```
root# netstat -rn                                                                                                                                          ✔  18:40:46  
Routing tables

Internet:
Destination        Gateway            Flags               Netif Expire
default            10.123.123.1.      UGScg                 en0
10.10.0/24         link#13            UCS                   en0      !
10.0.0.0/8         link#13            UCS                   en0      !
10.1.1.0/24        10.1.1.1.          UHLWI                 utun6  1007
```

In the example above you can see that the whole 10.0.0.0 network (with mask 255.0.0.0 eg /8) is routed through default device en0.

{% hint style="danger" %}
Because of the main routing 10.0.0.0/8 the VPN server routing the network 10.1.1.0/24 (which is included in 10.0.0.0/8) will not work.
{% endhint %}

### Firewall rulles

Another common problem is that **your server, on which the gateway is working, has some firewall rules that interfere with VPN network.** Please examine carefully `ufw` and `iptables` (even if ufw is disabled there may be `iptables` rules).

### VPN location ACLs

If a user has no access to the VPN location, sometimes admins forget that they **change the VPN settings and change a group that is allowed to access the VPN location**. If the user is not a part of that group which VPN location is configured to access:

<figure><img src="../../.gitbook/assets/Screenshot 2024-03-24 at 19.44.57.png" alt=""><figcaption></figcaption></figure>

will not be able to connect.

## I can access VPN but not my local network / Internet

Defguard only manages VPN server configuration (for now, we are planning ACLs / firewall management) which means it basically configures the VPN interface and peers.

Then when the client / user connects it actually establishes **a secure tunnel between their computer network and your server (that VPN interface)**.

From there, what happens to this traffic is the **administrator role.** The most common scenarios to do are:

* add routing rules, so that the traffic from that interface/VPN IP network gets routed to your network - this approach gives the advantage that users VPN ip persists in the network and the user is visible with it's VPN ip in your local network
* Masquerade or NAT - a common use case is to masquerade or NAT the traffic - which is **actually required if you want users to access Internet from the VPN -** this process is [described in detailed in this tutorial](../../tutorials/step-by-step-setting-up-a-vpn-server/#enabling-to-access-internet-through-your-vpn).

## Unable to sign in to your Defguard instance with correct credentials

The user tries to sign in to a Defguard instance but gets a 401 response with message "Session is required".

This issue is most likely caused by a misconfigured `DEFGUARD_URL` . Please have a look at the configuration options described in [General configuration](../../deployment-strategies/configuration.md#general-configuration) documentation.

If you want to access your Defguard instance without TLS (using an `http://` URL), please also make sure you have everything configured according to [Auth cookies configuration](../../deployment-strategies/configuration.md#auth-cookies-configuration) documentation.

## User lost access to their 2FA methods

If a user lost their TOTP app or security key and is therefore unable to log in, it is possible for an admin to disable MFA for their account.

In order to do this, go to the `Users` page and find the relevant user in the list. Then click on the cog button on the right and select `Disable MFA`. You will be then asked to confirm your choice.

It's recommended that after MFA is disabled, the user should configure a new MFA method as soon as possible.

## Unix socket permission errors when desktop client attempts to connect to VPN on Linux machines

To secure access to the background service which manages network interfaces (which requires elevated permissions) the Unix socket (`/var/run/defguard.socket`) used for gRPC communication is only accessible for users in the `defguard` group.

This means that the user who runs the GUI client must belong to this group. An incorrect setup results in `Permission denied` errors when attempting to connect to a VPN location.

By default the official packages (deb, rpm etc) should handle creating this group and adding the user, but in case of some unexpected errors it can also be done manually by running the following shell commands:

*   check if the `defguard` group exists: \\

    ```bash
    $ getent group defguard
    defguard:x:988:some_user  # this indicated that the group exists and user some_user is a member

    ```
*   if the group does not exist (you get no lines of output for the above command) create it manually:\\

    ```bash
    $ sudo groupadd -r defguard
    ```
*   add current user to the group:\\

    ```bash
    sudo usermod -a -G groupname $USER
    ```
* for the group membership changes to take effect you now need to reboot or log out and back in
*   confirm that your user is a member of `defguard` group:\\

    ```bash
    id -nG | grep -q defguard && echo "You are a member of defguard group" || echo "You are NOT a member of defguard group"
    ```
*   verify that the socket itself has correct permissions:\\

    ```bash
    $ ls -l /var/run/defguard.socket
    srw-rw---- 1 root defguard 0 Sep 15 15:02 /var/run/defguard.socket

    ```

## Unix socket does not exist. Error `No such file or directory (os error 2)` when trying to connect to a location

The socket file at `/var/run/defguard.socket` is created by the background service used by the client to manage network interfaces.

This service is usually run as a [systemd service](https://github.com/DefGuard/client/blob/f9c8d36166181aa3363957643baf793ce206043a/resources-linux/defguard-service.service) installed by a relevant package.

In case an issue occured during installation and the service is not enabled you can do it manually by running the following commands:

```bash
# Reload systemd to recognize new service file
sudo systemctl daemon-reload

# Enable service to start on boot
sudo systemctl enable defguard-service

# Start the service now
sudo systemctl start defguard-service
```

## Windows client is unable to connect

If the Windows client is unable to connect to establish a connection and shows command timeout errors it's useful to look into the application logs in the settings screen or in the [log files](../../using-defguard-for-end-users/desktop-client/#log-files) themselves.

If the background service logs contain errors similar to `Unable to access interface: No such file or directory` it might indicate that due to some unforeseen combination of factors a tunnel has not been shut down correctly.

To verify that this is the case open the system service manager and look for any services named `WireGuard Tunnel`

<figure><img src="../../.gitbook/assets/image (207).png" alt=""><figcaption></figcaption></figure>

If any such services are present, remove them and retry the connection.

## Desktop client high disk usage

Some users may experience unusually high disk activity from the Defguard desktop client, even when the app appears idle. This is almost always caused by SQLite performing full-table scans on large statistics tables.

### **1. Check Your Client Version**

The fix for the original table-scan problem was introduced in **v1.5.2**.

**Steps:**

* Open Defguard Desktop
* Look at the **bottom-left corner** → confirm the version is ≥ **v1.5.2**
* If not, download the latest version: [https://defguard.net/download/](https://defguard.net/download/)

### **2. Check the Database Size**

If the stats purge mechanism is not working correctly or heavy usage generates too much data, the database can grow very large.

**Location of the DB file: `C:\Users<YOUR_USERNAME>\AppData\Roaming\net.defguard\defguard.db`**

**Typical sizes:**

* Normal usage: a few MB
* Problematic: tens or hundreds of MB (e.g., 50–200 MB)

### **3. Temporary Workarounds**

{% hint style="danger" %}
Before trying any fix, **back up your database file**.
{% endhint %}

**Option A — Trim Stats Tables (safe & recommended)**

Install [SQLite](https://sqlite.org/index.html), open the DB, and run:

```sql
delete from location_stats;
delete from tunnel_stats;
```

This immediately reduces the DB size and disk I/O.

**Option B — Remove the Database Entirely (quickest, but requires re-enrollment)**

1. Close Defguard completely
2. Delete the database file: `C:\Users<YOUR_USERNAME>\AppData\Roaming\net.defguard\defguard.db`
3. Re-enroll your device in Defguard (you will need your enrollment link or admin setup)

This is the nuclear option but works reliably.

### **Long-Term Fix**

The stats subsystem is being **fully redesigned** in the upcoming **2.0** release.\
The new implementation will avoid these SQLite table-scan issues entirely.

## Client connects but cannot reach VPN servers

On some networks - especially mobile or carrier-grade networks - the MTU (Maximum Transmission Unit) may be lower than usual. When this happens, VPN packets can exceed the network's MTU and get dropped during transmission. This often results in partial connectivity issues or a complete failure to connect to VPN servers.

To address this, the Defguard client allows you to manually configure the MTU. Lowering the MTU reduces packet size so that traffic fits within the network’s limits and can be transmitted reliably.

As a general guideline, MTU values in the range of **1300-1500** work well in most cases. If you're experiencing connectivity problems, try lowering the MTU gradually within this range until the connection stabilizes.

<figure><img src="../../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

## Disconnecting from location show "Connection failed" system error

<figure><img src="../../.gitbook/assets/Screenshot 2026-01-09 at 12.23.18.png" alt=""><figcaption></figcaption></figure>

Issue occurs on Linux distributions with `NetworkManager` installed.

This error occurs because `NetworkManager` tries to manage Defguard network interfaces. This doesn't affect VPN connection and is a **visual** bug only.

To prevent this error from appearing, you can configure `NetworkManager` to stop managing network interfaces created by Defguard. To do so, we need to create a config for `NetworkManager`.

1. Create config file

```shellscript
sudo nano /etc/NetworkManager/conf.d/90-defguard.conf
```

2. Fill this file with this config. (Defguard tunnels start with `wg` by default)

```shellscript
[keyfile]
unmanaged-devices=interface-name:wg*
```

3. Save your file, and then restart `NetworkManager`.

```shellscript
sudo systemctl restart NetworkManager
```

## Command 'resolvconf' required by wireguard-rs couldn't be found.

On some Linux distributions (for example Debian 12 and 13), Defguard may fail to establish VPN tunnel after clicking "Connect" on a specified location. This is caused by lack of `resolvconf` command.

To resolve this issue, install missing dependency with:

```shellscript
sudo apt install openresolv
```

## Android app "Failed host lookup" error.

When connecting to a location with external OpendID provider, the application shows the following error: `DioException [connection error]: The connection errored: Failed host lookup`.

<figure><img src="../../.gitbook/assets/image (217).png" alt="" width="236"><figcaption></figcaption></figure>

This usually happens when aggressive battery optimization policy is used by the Android system, resulting in network connectivity issues and dns lookup errors. To fix it:

1. Go to Settings -> Apps -> App management -> Defguard -> Battery usage
2. Enable the "Allow background activity" switch.

Defguard app should now be able to connect to the server even when in background during the OpenID authentication.
