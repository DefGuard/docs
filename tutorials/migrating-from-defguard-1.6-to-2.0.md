# Migrating from Defguard 1.6 to 2.0

This guide explains how to migrate an existing Defguard 1.6 instance to Defguard 2.0.

It focuses on the migration process in the Core UI and assumes that you have already upgraded or are ready to upgrade your deployed Defguard components using your chosen deployment method.

If you prefer a video guide please watch the following:

{% embed url="https://youtu.be/qQFfd1Reoq4" %}

### Before you begin

Before starting the migration, make sure that:

* you have a working Defguard 1.6 instance
* your existing users, devices, Locations, and Gateways are working correctly
* you have a current backup of your database and configuration

### Upgrade Core to Defguard 2.0

Start by upgrading the Core component using your selected deployment method.

For example, this may mean:

* changing the Core container image tag
* upgrading a package

After Core starts in version 2.0, open the Core UI.

Defguard detects that the instance was upgraded from version 1.6 and starts the migration wizard automatically.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.00.34.png" alt=""><figcaption></figcaption></figure>

### Review detected configuration

Defguard reads your existing configuration from the previous 1.x setup.

This may include values previously stored in:

* environment variables
* configuration files
* existing database settings

The wizard displays the detected values so you can confirm them instead of entering everything again.

Review the displayed parameters carefully.

If the values are correct, continue.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.12.40.png" alt=""><figcaption></figcaption></figure>

### Create the internal Certificate Authority

Defguard 2.0 uses certificates to secure communication between components.

The migration wizard creates an internal Certificate Authority used to issue certificates for Defguard components, including:

* Edge
* Gateway

In Defguard 1.x, this type of configuration had to be handled manually. In Defguard 2.0, the wizard automates the process. We recommend using this option even if you already has that configured in your 1.6 setup.

Provide the required Certificate Authority details, such as the administrator email address, and continue.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.13.00.png" alt=""><figcaption></figcaption></figure>

You can also download the generated CA certificate if needed.

### Upgrade and adopt the Edge (formerly Proxy) component

The next step is upgrading and adopting the Edge component.

The wizard shows examples for running Edge using different deployment methods. Upgrade the former Proxy component to the Defguard 2.0 Edge component using the method appropriate for your environment.

Depending on your deployment, this may require updating the component image, package, service definition, or deployment manifest.

Make sure that Edge has persistent storage for certificates issued during adoption. For container-based deployments, this usually means configuring a persistent volume for Edge certificate data.

After the upgraded Edge component is running, return to the wizard and confirm that Edge has been deployed.

Then provide or confirm:

* Edge name, for example `Edge 1`
* Edge address reachable by Core
* Edge gRPC port

Defguard may prefill these values from your previous Proxy configuration. Verify that they are correct and continue.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.15.36.png" alt=""><figcaption></figcaption></figure>

Start the adoption process.

During adoption, Defguard issues certificates for Edge and establishes secure communication between Core and Edge.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.16.09.png" alt=""><figcaption></figcaption></figure>

After adoption succeeds, continue the migration.

### Review Core URL settings

Next, review the internal Core URL settings.

If your existing Core URL is still correct, you can leave it unchanged.

If your deployment already handles SSL through a reverse proxy or load balancer, you can keep that configuration and skip Defguard-managed SSL configuration.

Alternatively, Defguard can help configure SSL directly by:

* generating a certificate using the internal Certificate Authority
* using a certificate you upload manually

Choose the option that matches your deployment.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.19.20.png" alt=""><figcaption></figcaption></figure>

If you keep an existing reverse proxy setup, confirm that the reverse proxy configuration is correct and continue.

### Review Edge external URL settings

Next, review the external URL used to access Edge.

If your existing Proxy URL should continue to be used for Edge, leave it unchanged.

As with Core, you can either keep your existing SSL setup or configure SSL through Defguard, depending on your deployment.

For the Edge external URL, Defguard also supports obtaining a certificate automatically using Let’s Encrypt. This is useful if you want Defguard to handle certificate issuance and renewal.

When using Let’s Encrypt, make sure that the Edge external URL resolves to the Edge component and that the required HTTP and HTTPS ports are reachable by the certificate validation process.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.20.20.png" alt=""><figcaption></figcaption></figure>

If SSL is already handled by a reverse proxy or load balancer, confirm that the configuration is correct and continue.

After this step, the initial system configuration is complete.

### Migrate existing VPN Locations

The wizard then moves to Location migration.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.25.26.png" alt=""><figcaption></figcaption></figure>

Defguard detects the existing VPN Locations from your 1.6 instance and guides you through upgrading them.

Before migrating Gateways, review the networking change introduced in Defguard 2.0.

In Defguard 1.x, Gateway connected to Core. In Defguard 2.0, this communication is reversed

Because of this change, firewall rules may need to be updated.

### Update firewall rules for Gateway communication

Before adopting Gateways, make sure your firewall allows Core to connect to Gateway on the Gateway gRPC port.

By default, this is:

```
TCP 50066
```

You should allow this traffic only from Core to Gateway.

You should also remove rules that previously allowed Gateway to connect to Core on the old Core gRPC port.

The exact firewall configuration depends on your environment, cloud provider, and network architecture.

After updating firewall rules, confirm in the wizard that the Gateway firewall rules have been changed.

### Upgrade and adopt Gateway

For each migrated Location, Defguard guides you through upgrading and adopting the associated Gateway.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.33.10.png" alt=""><figcaption></figcaption></figure>

Upgrade the Gateway component to Defguard 2.0 using your chosen deployment method.

For example, this may mean:

* upgrading a package
* changing a container image tag
* applying an updated system service
* applying updated infrastructure automation

After the Gateway is upgraded and running, return to the Core UI and continue.

Provide or confirm:

* Gateway name, for example `Gateway 1`
* Gateway address reachable by Core
* Gateway gRPC port

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.33.47.png" alt=""><figcaption></figcaption></figure>

Start the Gateway adoption process.

During adoption, Defguard issues certificates for Gateway and establishes secure communication between Core and Gateway.

After adoption succeeds, the Gateway is connected to the migrated Location.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 15.34.11.png" alt=""><figcaption></figcaption></figure>

Repeat this process for each Location and Gateway if your deployment has more than one.

### Verify the migration

After the migration is complete, verify the migrated configuration in the Core UI.

Check the Locations view and confirm that:

* existing Locations are present
* Gateways are connected
* Location settings were preserved

Check the Edge components view and confirm that:

* the Edge component is connected
* the component is configured correctly

### Verify VPN connectivity

The migration should be transparent to end users.

Existing users should be able to connect using their existing device configuration.

To verify VPN connectivity:

1. Connect to the VPN using an existing device.
2. Confirm that the connection succeeds.
3. Open the VPN overview in the Core UI.
4. Confirm that the active connection is visible.

After completing the migration, your Defguard instance runs on the Defguard 2.0 architecture while preserving existing users, devices, and VPN Locations.
