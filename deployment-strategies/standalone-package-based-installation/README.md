---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/standalone-package-based-installation
---

# Standalone package based installation

## Introduction

This guide will walk you through the process of installing and running Defguard using system packages.

We will cover system requirements, additional dependencies, installation steps, and examples of configuration files and step by step running all services. In this example we will use NGINX for a web server (proxy) exposing and securing web based services.

{% hint style="info" %}
Make sure you understand [Defguard's architecture](../../in-depth/architecture/), especially the division into the main components: Core, Edge, Gateway.
{% endhint %}

{% hint style="warning" %}
This is a simple guide installing all components on a single server. For production make sure your infrastructure is prepared by following our [recommendations](../hardware-os-network-and-firewall-recommendations.md).
{% endhint %}

## System requirements

Before proceeding with the installation, ensure your system meets the following requirements:

* One of the installed:
  * [Debian](https://www.debian.org/)
  * [Ubuntu](https://ubuntu.com/)
  * [Fedora](https://fedoraproject.org/)
  * [Red Hat](https://www.redhat.com/)
  * [SUSE](https://www.suse.com/)
  * [FreeBSD](https://www.freebsd.org/)
  * [NetBSD](https://netbsd.org/)

* Administrative (sudo) privileges.

* A server with a public IP address (and you know what that IP address is and to which interface it’s assigned) – in this example we use: 185.33.37.51.

* You have a domain name and know how to assign IP and manage subdomains, in our example: Defguard main url will be _my-server.defguard.net_ (and the subdomain is pointed to 185.33.37.51).

* Defguard [enrollment service](https://defguard.gitbook.io/defguard/help/enrollment) (run by proxy) that will enable [remote onboarding, enrollment](https://defguard.gitbook.io/defguard/help/enrollment) and [easy configuration for our Desktop Clients (by adding Defguard instances)](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#adding-instance) with instance URL and one simple token – in this tutorial we use: _enroll.defguard.net_ (this subdomain also points to 185.33.37.51).

* If you have a **firewall**, we assume you have **opened port 443** in order to expose both Defguard and enrollment service, but also to automatically issue for these domains SSL certificates. Port 444 (used for internal gRPC communication) **should not be publicly exposed**.

* System clock is synchronized using [Network Time Protocol (NTP)](https://www.ntp.org/). This is important for time-based one-time password (TOTP) codes.

* PostgreSQL [Database](./database.md)

## Defguard packages

{% hint style="info" %}
Defguard also has a public APT repository, if you want know how to set it up, follow [this guide](defguard-apt-repository.md).
{% endhint %}

The following documents will guide you in package installation and configuration:

* [Defguard Core](./core.md)
* [Defguard Edge](./edge.md)
* [Defguard Gateway](./gateway.md)

### Reverse proxy

The reverse proxy acts as an intermediary between users and Defguard services, handling HTTPS requests, routing internal gRPC communication, and ensuring encrypted connections between all components.

Follow our additional guide on [configuring reverse proxy for for Core and Edge service](../reverse-proxy-configuration-using-nginx.md). After having the reverse proxy configured and running you can continue with this guide.

## Securing the setup

After the installation please make sure that **only the following ports are open on the server firewall:**

* HTTPS port for Defguard Edge (and/or the Defguard Core, if it should publicly available)
* VPN server port (eg. WireGuard port)

{% hint style="danger" %}
**DO NOT EXPOSE PUBLICLY THE gRPC ports of the core gateway and proxy, which are:**

* 444
* 50051
* 50055
{% endhint %}

Also this setup provides only communication encryption between Defguard components, if you additionally like for core/proxy and gateway to have authorization – [please setup a custom SSL CA](../grpc-ssl-communication.md#custom-ssl-ca-and-certificates).

## Upgrading packages

{% hint style="info" %}
If the new version introduces changes to the default configuration, the existing configuration file will not be overwritten. Instead, a separate file containing the updated default configuration will be created.
{% endhint %}

### Linux Debian/Ubuntu

Using `dpkg` with `-i` option (which handles both installation and upgrades for matching package names):

```sh
sudo dpkg -i defguard-X.Y.Z-x86_64-unknown-linux-gnu.deb
```

### Linux Fedora/Red Hat

Using `rpm`:

```sh
sudo rpm -Uvh defguard-X.Y.Z-x86_64-unknown-linux-gnu.rpm
```

#### FreeBSD/OPNsense

1. Uninstall the current version.

   ```sh
   # Core package
   pkg delete defguard

   # or Gateway package
   pkg delete defguard-gateway

   # or Proxy package
   pkg delete defguard-proxy
   ```

2. Install a newer version (as described [above](./#installing-packages)).

3. Restart the service.

   ```sh
   # Core service
   sudo /usr/local/etc/rc.d/defguard restart

   # or Gateway service
   sudo /usr/local/etc/rc.d/defguard_gateway restart

   # or Proxy service
   sudo /usr/local/etc/rc.d/defguard_proxy restart
   ```
