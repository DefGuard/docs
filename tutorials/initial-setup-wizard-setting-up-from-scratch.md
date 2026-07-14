# Initial Setup Wizard: setting up from scratch

This guide explains how to configure a fresh Defguard 2.0 instance using the Initial Setup Wizard in the Core UI.

It assumes that the Defguard stack has already been deployed using one of the supported deployment methods and that the Core UI is accessible in your browser.

If you prefer a video guide please watch the following:

{% embed url="https://youtu.be/prcBHClsHvU" %}

### Overview

The Initial Setup Wizard guides you through the first configuration of your Defguard instance.

During the process, you will:

* create the initial administrator account
* configure connection parameters
* create an internal Certificate Authority
* adopt the Edge component
* configure Core and Edge URLs
* configure TLS
* create the first VPN Location
* adopt the Gateway component
* verify that the VPN connection works

### Open the Core UI

Open the Defguard Core UI in your browser.

If this is a fresh instance, the Initial Setup Wizard starts automatically.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.32.48.png" alt=""><figcaption></figcaption></figure>

### Create the initial admin user

The first step is creating the initial administrator account.

Provide the required user details and continue.

This account will be used to complete the initial configuration and manage the Defguard instance.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.32.34.png" alt=""><figcaption></figcaption></figure>

### Create the internal Certificate Authority

Create a custom Certificate Authority.

This CA is used to issue certificates for Defguard components, including:

* Edge
* Gateway

It can also be used to issue a certificate for the Core UI if you want Defguard to handle TLS directly.

Provide:

* CA name
* administrator email address

After the CA is created, download the CA certificate.

If you use a certificate issued by this CA for the Core UI, import and trust it in your browser or operating system.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.33.15.png" alt=""><figcaption></figcaption></figure>

### Adopt the Edge component

The next step is adopting the Edge component.

Edge is the public-facing component used for enrollment and client-facing API access.

In the wizard:

1. Confirm that the Edge component has been deployed.
2. Provide a name for the Edge component
3. Provide the Edge address reachable by Core.
4. Start the adoption process.

During adoption, Defguard issues a certificate for Edge using the internal Certificate Authority.

This secures communication between Core and Edge.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.44.23.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.44.45.png" alt=""><figcaption></figcaption></figure>

### Configure the Core URL

Next, provide the URL used to access the Core UI.

This should be the final URL users and administrators will use to access Core, for example:

```
https://core.example.com
```

Depending on your deployment, this may be:

* an internal domain
* a private DNS name
* a URL exposed through a reverse proxy

Defguard will redirect you to the configured URL after the general configuration wizard.

### Configure SSL for Core

Choose how SSL should be configured for Core.

Available options include:

* skipping SSL configuration, useful if SSL is handled by a reverse proxy
* uploading your own certificate
* generating a certificate using Defguard’s internal Certificate Authority

If you generate a certificate using Defguard’s internal CA, download and trust the CA certificate on client machines that need to access Core.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.46.57.png" alt=""><figcaption></figcaption></figure>

### Configure the Edge external URL

Next, provide the external URL for Edge.

This is the URL used by users and clients to access enrollment and related services, for example:

```
https://edge.example.com
```

Make sure this URL points to the deployed Edge component.

### Configure SSL for Edge

Choose how SSL should be configured for Edge.

Available options include:

* skipping SSL configuration
* uploading your own certificate
* generating a certificate using Defguard’s internal Certificate Authority
* obtaining a certificate using Let’s Encrypt

If you choose Let’s Encrypt, make sure the required HTTP and HTTPS ports are reachable by the certificate validation process.

Defguard can then automatically obtain and renew the certificate.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.47.25.png" alt=""><figcaption></figcaption></figure>

After this step, the general system configuration is complete.

### Create the first VPN Location

After the initial system configuration, Defguard opens the Location wizard.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.49.52.png" alt=""><figcaption></figcaption></figure>

Provide a name for the Location, for example:

```
Office
```

Then configure the VPN endpoint:

* public IP address or DNS name that the clients will use to establish the VPN connection - depending on your deployment, this should be the address of the Gateway you configure in the following steps or it might be the address of a load balancer that's in front of your gateway cluster
* WireGuard UDP port used by clients

You can also configure:

* Internal VPN address
* Allowed IPs
* DNS settings
* default VPN Location settings
* MFA requirements
* group access
* firewall module settings

At the end of the wizard, keep the option enabled to activate the Location by setting up a Gateway.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.55.01.png" alt=""><figcaption></figcaption></figure>

### Adopt the Gateway component

The Gateway wizard guides you through connecting a Gateway to the Location.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.55.18.png" alt=""><figcaption></figcaption></figure>

In the wizard:

1. Confirm that the Gateway component has been deployed.
2. Provide a name for the Gateway.
3. Provide the Gateway address reachable by Core.
4. Start the adoption process.

During adoption, Defguard issues certificates for Gateway and secures communication between Core and Gateway.

After adoption, the Gateway is connected to the Location.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 13.56.51.png" alt=""><figcaption></figcaption></figure>

### Verify the setup

After the setup is complete, go to the Locations view. You should see the Location created and the Gateway connected.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 14.00.22.png" alt=""><figcaption></figcaption></figure>

Go to the Edge components view. You should see the Edge component connected.

<figure><img src="../.gitbook/assets/Screenshot 2026-04-30 at 14.00.46.png" alt=""><figcaption></figcaption></figure>

After completing the wizard, your Defguard instance is ready for further configuration, such as adding users, enabling MFA, configuring identity providers, and defining access policies.
