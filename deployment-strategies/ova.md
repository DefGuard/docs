# OVA

Defguard provides OVA images that can be imported into VMware, Proxmox, or any other solution that supports the standard OVA format. The image is based on Ubuntu 24.04 and supports configuration via `cloud-init`. It contains the full Defguard stack (Defguard Core, Edge, Gateway) and a PostgreSQL database, running as a Docker Compose stack.

The latest image can be downloaded here: [https://defguard-downloads.s3.eu-central-1.amazonaws.com/ova/defguard-latest.ova](https://defguard-downloads.s3.eu-central-1.amazonaws.com/ova/defguard-latest.ova)

{% hint style="info" %}
The image ships no reverse proxy. Defguard Edge terminates HTTPS itself on ports 80 and 443, using certificates from Let's Encrypt or from the built-in Defguard CA. See [Certificate management](../features/certificate-management.md) for details, and [#putting-defguard-behind-your-own-reverse-proxy](ova.md#putting-defguard-behind-your-own-reverse-proxy "mention") if you want to run your own proxy in front.
{% endhint %}

### Importing the image

After importing the image, make sure to:

1. Attach an appropriate network interface so the virtual machine can access your network.
2. If you would like to change the default user/password you can [do so with cloud-init](https://docs.cloud-init.io/en/latest/reference/yaml_examples/set_passwords.html) - if not, the default user `ubuntu` with password `ubuntu` will be created.

The image defaults to 2 vCPUs, 2 GB RAM and a 20 GB disk. Raise these to match your deployment before first boot.

### Setting up Defguard

Once booted, the virtual machine will have all Defguard components pre-configured. To complete the setup, simply visit the Defguard Core dashboard: http://\<VM\_IP\_OR\_DOMAIN>:8000. Follow the on-screen wizard to finalize your configuration.

The wizard is also where you configure HTTPS for Core and Edge, see [#https-and-certificates](ova.md#https-and-certificates "mention").

### Accessing the VM

You can access the VM using the following default credentials (requires changing after first login):

| Login    | `ubuntu` |
| -------- | -------- |
| Password | `ubuntu` |

{% hint style="info" %}
The `ubuntu` password is expired on first boot, so the password has to be changed at the console before SSH access (including key-based access) works for that account. If you inject your own user via `cloud-init`, this does not apply.
{% endhint %}

#### Verifying the running Defguard stack

When booting the machine for the first time, the Defguard stack is generated and launched by the `defguard-init.service` systemd unit. All Defguard files (Docker Compose file, environment variables, volumes) can be found under the `/opt/stacks/defguard/` directory:

| Path                                      | Contents                                                           |
| ----------------------------------------- | ------------------------------------------------------------------ |
| `/opt/stacks/defguard/docker-compose.yml` | the generated Compose file for this VM's selected components       |
| `/opt/stacks/defguard/.env`               | generated database password and component image tags               |
| `/opt/stacks/defguard/.volumes/`          | persistent data: database, Edge and Gateway certificates           |
| `/opt/stacks/defguard/init/`              | first-boot generator scripts and the Compose template (root-owned) |
| `/opt/defguard/`                          | the `dg-ctl` maintenance CLI, its `state.json` and its backups     |

To verify that Defguard is running, use the following command inside the VM:

```sh
sudo docker ps
```

On a default all-in-one VM you should see four containers: `defguard-core-1`, `defguard-edge-1`, `defguard-gateway-1` and `defguard-db-1`, plus `defguard-dockge-1` if you enabled [#dockge](ova.md#dockge "mention").

You can also run the built-in health checks, which verify every component of the deployed profile:

```sh
sudo dg-ctl test
```

Here is the breakdown of accessible services deployed on the VM:

<table><thead><tr><th>Name</th><th width="182">Port</th><th width="240">Type</th></tr></thead><tbody><tr><td>Core</td><td>8000</td><td>HTTP(S) (web dashboard)</td></tr><tr><td>Edge</td><td>8080</td><td>HTTP (enrollment portal)</td></tr><tr><td>Edge</td><td>443, 80</td><td>HTTPS and the Let's Encrypt ACME HTTP-01 challenge</td></tr><tr><td>Edge</td><td>50051</td><td>gRPC, published only in segmented deployments so Core can reach Edge from another host</td></tr><tr><td>Gateway</td><td>51820 (default)</td><td>UDP (VPN port). The Gateway runs on the host network, so the port is whatever you configure for the location in Core</td></tr><tr><td>Dockge</td><td>5001</td><td>HTTP (container management dashboard, disabled by default)</td></tr></tbody></table>

The database is not published to the host, it is only reachable from the Core container over the internal Docker network.

Only the Core dashboard should be exposed internally, never publicly. See [Architecture](../in-depth/architecture/) and [Hardware, OS, network and firewall recommendations](hardware-os-network-and-firewall-recommendations.md) for details.

### HTTPS and certificates

Defguard terminates TLS itself, so no reverse proxy is needed for HTTPS. During the setup wizard (or later, from Settings -> Certificates) you can:

- issue Core and Edge certificates from the built-in Defguard CA,
- upload your own certificate and private key,
- obtain a browser-trusted certificate for Edge from [Let's Encrypt](https://letsencrypt.org/).

{% hint style="info" %}
Edge and Gateway certificates are stored on persistent volumes under `/opt/stacks/defguard/.volumes/certs/`, so they survive container updates and `dg-ctl upgrade`.
{% endhint %}

Let's Encrypt issuance uses the ACME HTTP-01 challenge, which requires **TCP 80 on the Edge VM to be reachable from the internet** and the public Edge domain to resolve to it. The full flow is described in [Certificate management](../features/certificate-management.md).

#### Putting Defguard behind your own reverse proxy

If you already terminate TLS on your own reverse proxy or load balancer, set the Core and/or Edge certificate option to `none` and point your proxy at the ports listed above. A worked NGINX example is available in [Reverse Proxy configuration using NGINX](reverse-proxy-configuration-using-nginx.md).

You will need two domains: one for Defguard Core (internal) and one for Defguard Edge (public).

### Getting logs

Each container logs to the host journal under a `defguard-<component>` tag, so logs survive container restarts and updates.

#### Accessing logs via SSH

```sh
journalctl -t defguard-core -f
journalctl -t defguard-edge -f
journalctl -t defguard-gateway -f
journalctl -t defguard-db -f
```

The equivalent Compose view also works:

```sh
cd /opt/stacks/defguard/
sudo docker compose logs
```

First-boot output (stack generation and initial startup) is written to `/var/log/defguard-startup.log`.

#### Accessing logs via Dockge

1. Enable [Dockge (see chapter below).](ova.md#dockge)
2. Choose "defguard" in the left menu.
3. Go to the UI to the "Terminal" section:

<figure><img src="../.gitbook/assets/image (348).png" alt=""><figcaption></figcaption></figure>

### Upgrading

The OVA ships with `dg-ctl`, a maintenance CLI installed at `/opt/defguard/dg-ctl` and symlinked to `/usr/local/bin/dg-ctl`. Use it to update the VM. It takes a cold backup, applies pending structural migrations, regenerates the Compose file from the published template, pulls the new images, and health-tests the result. If any step fails, it rolls back to the backup automatically.

```sh
sudo dg-ctl version   # installed vs. available versions
sudo dg-ctl upgrade
```

The upgrade prints a plan (profiles, deployment mode, template ref, old -> new image tags and OVA version) and asks for confirmation before touching anything. Add `--yes` to run it unattended.

Available commands:

<table><thead><tr><th width="290">Command</th><th>What it does</th></tr></thead><tbody><tr><td><code>upgrade</code></td><td>Stop the stack, back it up, run pending migrations, refresh it from the release manifest, then health-test it</td></tr><tr><td><code>backup [--label L]</code></td><td>Cold full backup of volumes, structure, state and config</td></tr><tr><td><code>list-backups</code></td><td>Show local backups (id, creation time, OVA version, size)</td></tr><tr><td><code>rollback &#x3C;id></code></td><td>Restore a backup and bring that stack back up</td></tr><tr><td><code>test</code></td><td>Run the health checks against the running stack</td></tr><tr><td><code>version</code></td><td>Show installed and available versions</td></tr><tr><td><code>self-update</code></td><td>Replace the CLI itself with the published one</td></tr></tbody></table>

Useful options for `upgrade`: `--core-tag`, `--proxy-tag` and `--gateway-tag` to pin specific image tags, `--skip-tests` to skip the post-upgrade health checks, and `--no-backup` to skip the rollback point (only use this if you have an external backup and recovery plan).

Backups live in `/opt/defguard/backups`. After an upgrade, older ones are pruned to the 3 most recent, which you can change with the `KEEP_BACKUPS` environment variable.

{% hint style="warning" %}
`dg-ctl upgrade` stops the stack while it runs. Plan for a short outage.
{% endhint %}

#### Installing dg-ctl on an older OVA

Images built before OVA 2.1 do not include `dg-ctl`. Install it on a running VM with:

```sh
curl -fsSL https://raw.githubusercontent.com/DefGuard/deployment/main/ova/files/install-dg-ctl.sh | sudo bash
```

The installer is idempotent, seeds `/opt/defguard/state.json` from what is actually deployed, and installs the `jq`, `zstd` and `curl` dependencies if they are missing. Afterwards, run `sudo dg-ctl upgrade`, which will also migrate the older stack layout to the current one.

#### Updating containers manually

{% hint style="warning" %}
This only pulls new container images. It does not run OVA migrations and does not take a backup, so prefer `dg-ctl upgrade` unless you specifically want an image-only refresh.
{% endhint %}

{% hint style="info" %}
By default the generated Compose config uses a floating Docker image tag pinned to the latest stable major version, for example `2`.

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
DEFGUARD_CORE_TAG=2.1.0
DEFGUARD_PROXY_TAG=2.1.0
DEFGUARD_GATEWAY_TAG=2.1.0
```

Then use the same `docker compose` command as above to update the running containers. Note that `dg-ctl upgrade` overwrites these with the tags from the release manifest unless you pass `--core-tag`, `--proxy-tag` and `--gateway-tag`.

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

<table><thead><tr><th width="322">Profile</th><th width="411">What runs</th></tr></thead><tbody><tr><td>core</td><td>Core and the database</td></tr><tr><td>edge</td><td>Edge</td></tr><tr><td>gateway</td><td>Gateway</td></tr><tr><td>(no file)</td><td>Core, database, Edge and Gateway - the default all-in-one stack</td></tr></tbody></table>

Using a solution other than Proxmox will require creating a custom cloud-init that will write one of the profiles above to the `/opt/stacks/defguard/active-profiles` file.

{% hint style="info" %}
The file is consumed and deleted on first boot: the generated `/opt/stacks/defguard/docker-compose.yml` contains only the services for the selected profile. The selection is remembered in `/opt/stacks/defguard/init/.applied-profiles` so that `dg-ctl upgrade` regenerates the same set of components.

Because the components are split across hosts, a segmented deployment publishes Edge's gRPC port 50051 and leaves `DEFGUARD_ADOPT_EDGE` and `DEFGUARD_ADOPT_GATEWAY` empty in `.env`, so you adopt Edge and Gateway from the Core wizard instead.
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
