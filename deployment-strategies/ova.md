# OVA

Defguard provides OVA images that can be imported into VMware, Proxmox, or any other solution that supports the standard OVA format. The image is based on Ubuntu 24 and supports configuration via `cloud-init`. It contains the full Defguard stack (Defguard Core, Edge, Gateway) and a database.

The latest image can be downloaded here: [https://defguard-downloads.s3.eu-central-1.amazonaws.com/ova/defguard-latest.ova](https://defguard-downloads.s3.eu-central-1.amazonaws.com/ova/defguard-latest.ova)

### Importing the image

After importing the image, make sure to:

1. Attach an appropriate network interface so the virtual machine can access your network.
2. If you would like to change default user/password you can [do so with cloud-init](https://docs.cloud-init.io/en/latest/reference/yaml_examples/set_passwords.html) - if not, default user ubuntu with pass ubuntu will be created.

{% hint style="info" %}
If you're importing the OVA into VMware, the image ships with `open-vm-tools` pre-installed, which enables graceful shutdown, guest IP reporting, and time sync with the host.
{% endhint %}

### Setting up Defguard

Once booted, the virtual machine will have all Defguard components pre-configured. To complete the setup, simply visit the Defguard Core dashboard: http://\<VM\_IP\_OR\_DOMAIN>:8000. Follow the on-screen wizard to finalize your configuration.

If you would like to configure a domain and automated SSL certificates via Let's Encrypt beforehand, [go to this section for more details](ova.md#setting-up-ssl).

### Accessing the VM

You can access the VM using the following default credentials (requires changing after first login):

| Login    | `ubuntu` |
| -------- | -------- |
| Password | `ubuntu` |

#### Verifying the running Defguard stack

When booting the machine for the first time, the whole Defguard stack will be launched using Docker Compose. All Defguard files (Docker compose, environment variables) can be found under the `/opt/stacks/defguard/` directory.

On first boot, the VM generates a random database password into `.env` and starts the stack; this typically completes within about a minute. If the dashboard isn't reachable yet, or you want to confirm what happened during first boot, check the startup log:

```sh
cat /var/log/defguard-startup.log
```

To verify that Defguard is running, use the following command inside the VM:

```sh
sudo docker ps
```

<figure><img src="../.gitbook/assets/obraz.png" alt=""><figcaption></figcaption></figure>

Here is the breakdown of accessible services deployed on the VM:

<table><thead><tr><th>Name</th><th width="182">Port</th><th width="240">Type</th></tr></thead><tbody><tr><td>Core</td><td>8000</td><td>HTTP (web dashboard)</td></tr><tr><td>Edge</td><td>8080, 80, 443</td><td>HTTP (enrollment portal) and HTTP(S) once a domain and SSL are configured</td></tr><tr><td>Gateway</td><td>51820</td><td>UDP (VPN port)</td></tr></tbody></table>

{% hint style="info" %}
Dockge (if enabled, see [below](ova.md#dockge)) additionally exposes its management dashboard on port 5001.
{% endhint %}

### VPN client internet access

The OVA runs Gateway on a host that also runs Docker. If VPN clients should reach the internet through the VM, you usually need all of the following:

- IP forwarding enabled on the host, for both IPv4 and IPv6.
- A masquerade rule for the VPN subnet.
- `DOCKER-USER` allow rules for the WireGuard interface when Docker sets `FORWARD` to `drop`.

For manual host configuration, the required `iptables` rules look like this:

```sh
sudo iptables -I DOCKER-USER -i wg0 -j ACCEPT
sudo iptables -I DOCKER-USER -o wg0 -j ACCEPT
sudo iptables -t nat -A POSTROUTING -s <VPN_SUBNET> -o <EGRESS_INTERFACE> -j MASQUERADE
```

Replace `wg0`, `<VPN_SUBNET>`, and `<EGRESS_INTERFACE>` with values from your deployment.

{% hint style="warning" %}
Docker enables `net.ipv4.ip_forward` itself, but **not** IPv6 forwarding. If your deployment routes IPv6 traffic through the VPN, also set `net.ipv6.conf.all.forwarding = 1` on the host.
{% endhint %}

These host firewall rules are not persistent by default. On the OVA, save them with:

```sh
sudo apt install iptables-persistent
sudo netfilter-persistent save
```

The OVA itself applies the `DOCKER-USER` rules automatically at every boot, once Docker is up. Because the `DOCKER-USER` chain only exists after the Docker daemon has finished starting, the OVA's firewall service waits briefly for it to appear; if Docker isn't ready in time, the service exits cleanly and the rules are re-applied on the next boot. If VPN clients briefly can't reach the internet right after a boot but everything works after a reboot, this ordering is the likely cause, see [Troubleshooting](ova.md#troubleshooting) below.

If you prefer Defguard Gateway to manage the source NAT automatically, enable `DEFGUARD_MASQUERADE=true` for the Gateway service and redeploy the stack. This is usually the simplest option for the OVA.

For the generic routing and troubleshooting guidance, see [Can access VPN but not local network or internet](../support-1/troubleshooting-guides/can-access-vpn-but-not-local-network-or-internet.md).

### Kernel and network tuning

The OVA ships with WireGuard-oriented kernel tuning applied out of the box, sized for the baseline 2 vCPU / 2 GB appliance and up to roughly 100 active devices:

- BBR congestion control (`net.ipv4.tcp_congestion_control = bbr`) to reduce bufferbloat.
- Enlarged UDP socket buffers (`net.core.rmem_max` / `wmem_max = 16777216`), since WireGuard is UDP-based and the OS defaults are too small for 1 Gbps+ links.
- A larger kernel input queue and accept queue (`net.core.netdev_max_backlog = 5000`, `net.core.somaxconn = 8192`) to absorb traffic bursts.
- An increased connection-tracking table (`net.netfilter.nf_conntrack_max = 131072`) for VPN egress/masquerade.

If you scale the VM beyond the baseline (more CPU/RAM for more concurrent devices), consider raising `nf_conntrack_max` and the socket buffer sizes accordingly. See [Linux kernel WireGuard tuning](linux-kernel-wireguard-tuning.md) for the full background on these parameters.

### Resource sizing

The OVA defaults to **2 vCPU, 2 GB RAM, and a 20 GB disk**, which comfortably supports up to roughly 100 active WireGuard devices. This is a baseline, not a hard limit, so as a rule of thumb, plan for additional CPU and RAM as the number of active devices grows well beyond that, similar to the guidance in [Hardware, OS, network and firewall recommendations](hardware-os-network-and-firewall-recommendations.md). vCPU/RAM can be resized after first boot through your hypervisor; disk usage should be monitored as the database and logs grow.

### Getting logs

You can access all components logs by remote access via SSH and in the Dockage UI.

#### Accessing logs via SSH

To access logs for all components access the VM via SSH (see above) and:

```
cd /opt/stacks/defguard/
docker compose logs
```

#### Accessing logs via Dockage

1. Enable[ Dockage (see chapter below).](ova.md#dockge)
2. Choose "defguard" in the left menu.
3. Go to the UI to the "Terminal" section:

<figure><img src="../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

### Setting up SSL

{% hint style="success" %}
Defguard Edge terminates HTTP/HTTPS directly on ports 80/443 and can automatically obtain certificates from [https://letsencrypt.org/](https://letsencrypt.org/) (or use your own certificates instead) - no separate reverse proxy container is bundled on the OVA.
{% endhint %}

{% hint style="info" %}
You'll need a public domain pointed at the VM for Defguard Edge. Defguard Core does not need to be publicly reachable, it does not need a public domain either. See [Architecture](../in-depth/architecture/) for details.
{% endhint %}

Domain and certificate configuration is done from the Defguard Core dashboard, not on the VM itself: set Edge's public domain in Core's settings, and Core will trigger the Let's Encrypt HTTP-01 challenge (over port 80) and push the issued certificate to Edge automatically. If you'd rather use your own certificate, upload it in Core instead of enabling Let's Encrypt.

Please make sure you don't expose Defguard Core publicly.

If you still want to put your own reverse proxy in front of the OVA (for example to centralize TLS termination for several services), you can do so, but you'll need to deploy and manage it yourself; it is not part of the OVA image.

### UPGRADING

{% hint style="warning" %}
By default the docker compose config included by the OVA uses a floating Docker image tag pinned to the latest stable major version, for example `2`.

This means that updating the containers like described below will fetch the latest release matching this major version, for example `2.1.2`, `2.3.1` or whatever is the latest release at the given time.

If you'd like to explicitly control the component versions see the instructions [below](ova.md#using-specific-image-tags).
{% endhint %}

Containers can be updated using the following commands in the `/opt/stacks/defguard` directory:

```sh
sudo docker compose pull
sudo docker compose rm -sf
sudo docker compose up -d
```

This can also be achieved without accessing the VM using the Dockge dashboard, refer to [this section](ova.md#dockge) for more information.

#### Using specific image tags

If you'd like to manually set the specific Docker image tags used by each component you can edit the `.env` file found in `/opt/stacks/defguard` and update the following environment variables:

```
DEFGUARD_CORE_TAG=2.0.0
DEFGUARD_PROXY_TAG=2.0.0
DEFGUARD_GATEWAY_TAG=2.0.0
```

Then use the same `docker compose` command as above to update the running containers.

## Cloud-Init options

### Selecting what components to run (Proxmox)

As mentioned previously, the VM starts the full stack by default. If you would like to separate the components (which is the recommended [way of deploying Defguard](../in-depth/architecture/)), you can use custom `cloud-init` configuration to specify which component to run for a given VM instance.

Create the following snippet. The content can be `core`, `edge`, or `gateway`:

```
#cloud-config
write_files:
  - path: /opt/stacks/defguard/active-profiles
    content: "core"
```

In Proxmox, save the snippet to (or your selected snippet directory, if you are using a non-standard one):

```
/var/lib/vz/snippets/defguard-core-userdata.yaml
```

Then attach it to the VM on which you want to run the selected Defguard component:

```sh
qm set <ID_OF_THE_VM> --cicustom "vendor=local:snippets/defguard-core-userdata.yaml"
```

Next, boot the VM. Now, only the selected component should run.

Here is the full breakdown of what runs for each profile:

<table><thead><tr><th width="322">Profile</th><th width="411">What runs</th></tr></thead><tbody><tr><td>core</td><td>Core, database</td></tr><tr><td>edge</td><td>Edge</td></tr><tr><td>gateway</td><td>Gateway</td></tr></tbody></table>

Using different solution that Proxmox will require creating a custom cloud-init that will write one of the profiles above to the `/opt/stacks/defguard/active-profiles` file.

{% hint style="info" %}
The `write_files` snippet itself is standard `cloud-init` user-data and isn't Proxmox-specific. The same pattern works on AWS, Azure, or any other platform that supports cloud-init; only the way you deliver the user-data (a Proxmox snippet vs. a provider's user-data field) differs.
{% endhint %}

{% hint style="warning" %}
`active-profiles` is only read once, on first boot: the VM flattens it into `/opt/stacks/defguard/docker-compose.yml` and then deletes `active-profiles` (same for `enable-docker-management`, see [Dockge](ova.md#dockge)). Attach your cloud-init snippet **before** first boot. To change the selected components afterward, write the new `active-profiles` file yourself, then:

```sh
sudo rm /opt/stacks/defguard/docker-compose.yml
sudo systemctl restart defguard-init.service
```
{% endhint %}

{% hint style="info" %}
For a segmented deployment (`edge` or `gateway` alone, without `core` on the same VM), Core's automatic adopt targets for Edge/Gateway are left blank in `.env` since they live on other VMs; set `DEFGUARD_ADOPT_EDGE` and `DEFGUARD_ADOPT_GATEWAY` there yourself before starting Core. This only applies to the segmented case, on a full all-in-one VM they're filled in automatically.
{% endhint %}

### Dockge

You can additionally enable [Dockge](https://github.com/louislam/dockge) to easily manage and update all Defguard containers. To do so, add the following to your cloud-init snippet (this was explained more in-depth in the [#selecting-what-components-to-run-proxmox](ova.md#selecting-what-components-to-run-proxmox "mention") section):

```
#cloud-config
write_files:
  - path: /opt/stacks/defguard/enable-docker-management
    content: ""
```

After the virtual machine starts, Dockge dashboard should be available at `http://<VM_IP_OR_DOMAIN>:5001` . Access it in order to create a Dockge admin account.

#### Updating containers with Dockge

If you've enabled Dockge you can use it to update the running containers by opening the dashboard, selecting the `defguard` stack and clicking `Update`:

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

To set a specific Docker image tag you can instead click the `Edit` button, then scroll down to the `.env` section and edit the highlighted environment variables:<br>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Once the variables are set you can scroll back up and click the `Deploy` button:

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Backup

All state that needs backing up on the OVA lives under `/opt/stacks/defguard/`:

- `.env` – generated secrets (database password, image tags). Losing this without a backup means the database password no longer matches unless you regenerate it consistently.
- `.volumes/db` – the PostgreSQL database. Prefer a regular `pg_dump` over a filesystem-level copy, consistent with the general [backup strategy](hardware-os-network-and-firewall-recommendations.md#backup-strategy).
- `.volumes/certs/*` – the SSL certificates used to secure and authenticate communication between Core, Edge, and Gateway. These are issued by Defguard's internal CA and are not stored in the database, so they must be backed up separately.

## Troubleshooting

**VPN clients can't reach the internet right after boot, but it works after a reboot.** The OVA applies `DOCKER-USER` forwarding rules automatically once Docker is ready; on a slow boot this can race Docker's startup. Check `/var/log/defguard-startup.log` for a `DOCKER-USER chain not present` message, and see [VPN client internet access](ova.md#vpn-client-internet-access) above.

**SSL certificate errors.** Confirm Edge's domain is set correctly in Core as described in [Setting up SSL](ova.md#setting-up-ssl), that DNS for that domain resolves to the VM, and that the automatic Let's Encrypt HTTP-01 challenge on port 80 isn't blocked by an upstream firewall.

**Changed `active-profiles` but nothing happened.** It's only applied when `/opt/stacks/defguard/docker-compose.yml` doesn't exist yet (first boot). To change the running components afterward, remove `docker-compose.yml` and restart `defguard-init.service`, see [Selecting what components to run](ova.md#selecting-what-components-to-run-proxmox). Image tags in `.env` don't have this restriction, see [Using specific image tags](ova.md#using-specific-image-tags).

**Dockge dashboard isn't reachable at port 5001.** Dockge is opt-in: confirm the `enable-docker-management` file was written via cloud-init as described in [Dockge](ova.md#dockge), and that the VM has finished booting.

**Dashboard doesn't come up after first boot, or `.env` looks wrong.** Check `/var/log/defguard-startup.log` for errors from secret generation or stack startup, and confirm `sudo docker ps` shows all expected containers.
