# Core cannot connect to Gateway or Edge after restart on single-host deployments

### Problem

In single-host deployments where Defguard Core, Gateway, and Edge are installed on the same machine using standalone packages, Core may fail to connect to either Gateway or Edge after a restart.

{% hint style="warning" %}
Running all Defguard components on the same server is not recemmended. Please follow [these recommendations](../../../deployment-strategies/hardware-os-network-and-firewall-recommendations.md) in you productions environments.
{% endhint %}

### Symptoms

After rebooting or restarting the services, Core logs may repeatedly show errors similar to:

```
Failed to connect to Gateway
The request does not have valid authentication credentials
Client certificate serial mismatch
```

or the same error may appear for Edge.

The affected component is usually the one that was adopted first. For example:

* if Gateway was adopted first and Edge second, Gateway may fail after restart;
* if Edge was adopted first and Gateway second, Edge may fail after restart.

Both components may work correctly immediately after adoption. The issue usually appears only after a restart.

### Cause

By default, both Gateway and Edge may use the same certificate directory:

```
/etc/defguard/certs
```

When Gateway and Edge run on the same host and share this directory, the second adoption can overwrite certificate files required by the first adopted component.

In particular, both services may use the same Core client certificate filename:

```
core_client_cert.pem
```

As a result, after restart, the first adopted component can no longer authenticate Core, causing a client certificate mismatch.

### Workaround

Configure separate certificate directories for Gateway and Edge.

#### Gateway

Edit:

```
/etc/defguard/gateway.toml
```

Set:

```toml
cert_dir = "/etc/defguard/certs-gateway"
```

#### Edge

Edit:

```
/etc/defguard/proxy.toml
```

Set:

```toml
cert_dir = "/etc/defguard/certs-edge"
```

Then restart the services:

```bash
sudo systemctl restart defguard-gateway
sudo systemctl restart defguard-proxy
sudo systemctl restart defguard
```

Depending on the current state of the certificates, you may need to re-adopt the affected Gateway or Edge component after changing the certificate directories.
