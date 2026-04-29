---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/standalone-package-based-installation
---

# Standalone package based installation

## Introduction

This guide walks you through the process of installing and running Defguard using system packages.

We will cover system requirements, additional dependencies, installation steps, example configuration files, and the step-by-step process of running all services. In this example, we use NGINX as a web server (proxy) to expose and secure web-based services.

This deployment method is a good fit if you want to run Defguard directly on a host without introducing container orchestration or a Docker-based workflow. It gives you straightforward access to the system services, configuration files, package manager, and logs, which can make the setup easier to understand and operate in smaller environments.

The guide is written as a practical single-server example, but the same package-based approach can also be adapted to more structured environments where Core, Edge, Gateway, and the database are separated across multiple machines.

{% hint style="info" %}
Make sure you understand [Defguard's architecture](../../in-depth/architecture/), especially the division into the main components: Core, Edge, and Gateway.
{% endhint %}

{% hint style="warning" %}
This is a simple guide for installing all components on a single server. For production, make sure your infrastructure is prepared by following our [recommendations](../hardware-os-network-and-firewall-recommendations.md).
{% endhint %}

## System requirements

Before proceeding with the installation, ensure your system meets the following requirements:

These prerequisites are meant to help you avoid the most common deployment issues, especially around DNS, certificates, public reachability, and service-to-service communication.

* One of the following operating systems installed:
  * [Debian](https://www.debian.org/)
  * [Ubuntu](https://ubuntu.com/)
  * [Fedora](https://fedoraproject.org/)
  * [Red Hat](https://www.redhat.com/)
  * [SUSE](https://www.suse.com/)
  * [FreeBSD](https://www.freebsd.org/)
  * [NetBSD](https://netbsd.org/)

* Administrative (sudo) privileges.

* A server with a public IP address (and knowledge of what that IP address is and which interface it is assigned to) - in this example, we use `185.33.37.51`.

* A domain name, and knowledge of how to assign IP addresses and manage subdomains. In our example, the main Defguard URL is _my-server.defguard.net_ (and the subdomain points to `185.33.37.51`).

* A Defguard [enrollment service](https://defguard.gitbook.io/defguard/help/enrollment) (run by the proxy) that enables [remote onboarding and enrollment](https://defguard.gitbook.io/defguard/help/enrollment), as well as [easy configuration for our Desktop Clients (by adding Defguard instances)](../../using-defguard-for-end-users/desktop-client/instance-configuration.md#adding-instance) using the instance URL and a simple token. In this tutorial, we use _enroll.defguard.net_ (this subdomain also points to `185.33.37.51`).

* If you have a **firewall**, we assume you have **opened port 443** in order to expose both Defguard and the enrollment service, and to automatically issue SSL certificates for these domains. Port 444 (used for internal gRPC communication) **should not be publicly exposed**.

* System clock is synchronized using [Network Time Protocol (NTP)](https://www.ntp.org/). This is important for time-based one-time password (TOTP) codes.

* PostgreSQL [Database](./database.md)

## Defguard packages

{% hint style="info" %}
Defguard also has a public APT repository. If you want to know how to set it up, follow [this guide](defguard-apt-repository.md).
{% endhint %}

The following documents will guide you in package installation and configuration:

* [Defguard Core](./core.md)
* [Defguard Edge](./edge.md)
* [Defguard Gateway](./gateway.md)

Install the components in the recommended order from the main deployment overview: prepare the database, install Edge, install Gateway, and then install and configure Core. Following that order makes it easier to connect the components correctly and validate each step as you go.

### Reverse proxy

The reverse proxy acts as an intermediary between users and Defguard services, handling HTTPS requests, routing internal gRPC communication, and ensuring encrypted connections between all components.

Follow our additional guide on [configuring a reverse proxy for the Core and Edge services](../reverse-proxy-configuration-using-nginx.md). After the reverse proxy is configured and running, you can continue with this guide.

In most environments, the reverse proxy is also the place where you terminate TLS, manage certificates, and expose only the endpoints that should be publicly reachable. Keeping that layer configured correctly is an important part of securing the whole deployment.

## Securing the setup

After the installation, please make sure that **only the following ports are open on the server firewall:**

* HTTPS port for Defguard Edge (and/or Defguard Core, if it should be publicly available)
* VPN server port (e.g. a WireGuard port)

{% hint style="danger" %}
**DO NOT EXPOSE PUBLICLY THE gRPC ports of the core gateway and proxy, which are:**

* 444
* 50051
* 50055
{% endhint %}

After the services are running, it is a good idea to verify not only that the expected public endpoints are reachable, but also that the internal service ports are accessible only from the systems that actually need them. This reduces the attack surface and helps keep the deployment aligned with the production recommendations.

## Upgrading packages

{% hint style="info" %}
If the new version introduces changes to the default configuration, the existing configuration file will not be overwritten. Instead, a separate file containing the updated default configuration is created.
{% endhint %}

Before upgrading, review the release notes, back up your database and configuration files, and confirm whether the new version introduces any configuration changes or migration-related considerations. After the upgrade, restart the relevant services and verify that Core, Edge, Gateway, and the reverse proxy can still communicate correctly.

### Linux Debian/Ubuntu

Using `dpkg` with the `-i` option (which handles both installation and upgrades for matching package names):

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
