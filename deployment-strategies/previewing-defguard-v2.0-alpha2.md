# Previewing Defguard v2.0-alpha2

This tutorial will help you test the new major update to Defguard - version 2.0.

For the list of changes made in this version, go to our release blog post or release notes.

{% hint style="danger" %}
This is an early alpha! Do not migrate from your previous production versions to this one! This release is only for setting up new, test instances. It's meant to preview the upcoming v2.0 and gather feedback.
{% endhint %}

## Starting Defguard 2.0

We've prepared a convenient docker compose config file that allows you to easily set up the whole Defguard stack and test the new UI and functionalities.

To start the Defguard v2.0 stack, do the following:

```
git clone https://github.com/DefGuard/deployment.git defguard-deployment
cd defguard-deployment/docker-compose2.0
docker compose up -d
```

This will start 8 docker containers:

* db - PostgreSQL database
* core - Defguard Core component (main control plane)
* edge1 - Defguard Edge (formerly Proxy) component
* gateway1 - Defguard Gateway components (VPN gateway)

## Initial configuration wizard

Notice that the Docker Compose file contains only minimal configuration parameters. This is one of the major changes in the new version. All configuration that was previously stored in environment variables or configuration files is now stored in the database and initialized using a convenient **setup wizard**.

To begin the initial configuration, just visit this address http://localhost:8000/ after you started the stack with Docker Compose. Defguard will detect that this is a fresh instance and will welcome you with the setup wizard.

The setup process contains several major steps:

* Creating the first admin user
* Internal and external URL settings
* VPN public and internal settings
* Multi-factor authentication

For the stable version of 2.0 we'll also add a **migration wizard** that will help you to upgrade from previous Defguard version to the latest one with ease.

### Example setup

{% stepper %}
{% step %}
**Go to the Core Component UI**

Visit [http://localhost:8000](http://localhost:8000/) after starting the stack using Docker Compose. See the Initial Setup Wizard is being triggered automatically. Also notice that the Edge and Gateway component where automatically adopted.<br>

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 20.59.40.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Create admin user account**

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.00.07.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Provide internal and external URL settings**

You can set `http://localhost:8000` as the Defguard URL and `http://localhost:8080` as the _Public Edge Component URL._

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.00.27.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Configure external and internal VPN settings**

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.01.05.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Configure multi-factor authentication**

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.01.10.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Setup finished**

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.01.14.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Inspect the newly created Edge Component, Location and Gateway Component**

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.42.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.37.png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Enjoy fully redesigned interface

After finishing the initial setup, Defguard is fully operational. You can manage your instance using the fully redesigned UI/UX.

You can also enrol users and connect to the newly crated Location.

You'll notice changes in every part of the interface, but some areas changed in a very significant way. Check those modules for sure:

* VPN overview - strictly a dashboard for the administrator, previously mixed with system configuration, which was confusing. Also, we've significantly refactored our statistics module to make sure the dashboard is responsive even for large deployments.
* Dedicated Locations page - previously hidden somewhere in the VPN overview page, mixed with dashboard, now a clear Location listing and management.
* Firewall (formerly ACL) - new nomenclature (Aliases, Destinations, Rules), brand-new Alias, Destination, and Rule form. The Rule form, despite realising a complex task of creating a firewall rule, is intuitive and guides the user through the process.
* Settings - since all the settings are now stored in the database, they can be managed with the UI. All system parameter got divided into logical sections, with broad descriptions, making it much easier to configure your system.
* Edge Components (formerly Proxy) page - brand-new page for managing Edge Components (exposing selected Core functionality to the internet while keeping the Core isolated).

## High Availability of Edge and Gateway components

{% hint style="info" %}
This is an Enterprise feature. [Enroll into Defguard PoC](https://defguard.net/evaluation-license/) and receive a 30 day Defguard Trial license with evaluation support.
{% endhint %}

Another major feature of v2.0 is High Availability in active-active mode for the Edge and Gateway components.

You can now add multiple Gateways to your Locations. Users will still connect to a single Gateway (using sticky sessions), but in the event of a Gateway failure, their VPN connection will remain active and be handled by another Gateway.

You can also add multiple Edge components to ensure that enrollment, configuration updates, and MFA session initiation are fast and fail-safe.

### Example setup

{% stepper %}
{% step %}
**Start the High Availability stack**

To start the Defguard v2.0 HA stack, do the following:

```
git clone https://github.com/DefGuard/deployment.git defguard-deployment
cd defguard-deployment/docker-compose2.0
docker compose -f docker-compose.ha.yaml up -d
```

This will start 8 docker containers:

* db - PostgreSQL database
* core - Defguard Core component (main control plane)
* edge1, edge2, edge-lb - two Defguard Edge (formerly Proxy) components with a NGINX-based load balancer (user enrolment and client app configuration)
* gateway1, gateway2, gateway-lb - two Defguard Gateway components with an Envoy-based load balancer (VPN gateways)
{% endstep %}

{% step %}
**Do the initial configuration**

Follow the same steps as in the [basic example](previewing-defguard-v2.0-alpha2.md#initial-configuration). You will then have a Defguard instance with a single Gateway and Edge component configured.
{% endstep %}

{% step %}
**Enter your Enterprise licence key**

Go to Settings -> License and enter your license key. [Enroll into Defguard PoC](https://defguard.net/evaluation-license/) and receive a 30 day Defguard Trial license if you don't have a key yet.
{% endstep %}

{% step %}
**Add another Edge Component**

If you've started the all the services from the provided Docker Compose configuration, the additional Edge Compoent service is already started and waiting to be adopted in Defguard Core. Both Edge Components are behind a basic NGINX-based load balancer.

Use the Docker service name as the _IP or Domain_ while configuring the component.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.37.27.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.38.28.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.38.31.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.38.39.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
**Add another Gateway Component to your Location**

If you've started the all the services from the provided Docker Compose configuration, the additional Gateway Component service is already started and waiting to be adopted in Defguard Core. Both Gateway Components are behind a basic Envoy-based load balancer.

Use the Docker service name as the _IP or Domain_ while configuring the component.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.40.06.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.41.04.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.41.07.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.42.14.png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

### Test the High Availability and Failover

#### Test HA for Edge Components

1. Display logs of both Edge Components using `docker compose logs -f edge1 edge2`
2. Trigger enrolment or MFA VPN connection using the Defguard Desktop or Mobile Application.
3. Notice traffic being directed to both Edge Components using round robin strategy on the NGINX load balancer.
4. Stop one of the Edge Components using `docker compose stop edge1`
5. Notice the enrolment process or MFA VPN connections working as expected on the Edge Components that's left.

#### Test HA for Gateway Components

1. Display logs of both Gateway Components using `docker compose logs -f gateway1 gateway2`
2. Ping the VPN gateway using `ping 10.10.10.1`. Since the VPN connection is not active yet it should fail. Keep it running through the test.
3. Connect to the VPN Location.
4. Notice that the ping now succeeds.
5. Now play with stoping on of the gateways `docker compose stop gateway1` or `docker compose stop gateway2` . Make sure you don't stop both of them.
6. Notice that the VPN connection is alive the whole time (ping still succeeds).

## Static IP assignment for devices

Another hihly anticipated feature of the 2.0 is the Static IP assignment. System administrators can now assign static IPs for the selected devices in the selected networks.

Start by going to the users list. You'll notice that there's a new item in the actions menu for a user.

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.33.21.png" alt=""><figcaption></figcaption></figure>

The administrator will then see a modal window with all the devices of the sleected user in each Location configured in the system. He can now enter the desired IP address for a device in any of the selected Locations.

<figure><img src="../.gitbook/assets/Screenshot 2026-03-12 at 21.33.28.png" alt=""><figcaption></figcaption></figure>
