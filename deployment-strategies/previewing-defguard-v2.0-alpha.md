# Previewing Defguard v2.0-alpha

This tutorial will help you test the new major update to Defguard - version 2.0.

For the list of changes made in this version go to our release blog post or release notes.

{% hint style="danger" %}
This is an early alpha! Do not migrate from your previous production versions to this one! This release is only for setting up new, test instances. It's meant to preview the upcoming v2.0 and gather feedback.
{% endhint %}

## Starting Defguard 2.0

We've prepared a convenient docker compose config file that allows you to easily set up the whole Defguard stack and test the new UI and functionalities.

To start the Defguard v2.0 stack do the following:

```
git clone git@github.com:DefGuard/deployment.git -b add-compose-for-testing-2.0 defguard-deployment
cd defguard-deployment/docker-compose2.0
docker compose up -d
```

This will start 8 docker containers:

* db - PostgreSQL database
* core - Defguard Core component (main controll plane)
* edge1, edge2, edge-lb - two Defguard Edge (formerly Proxy) components with a NGINX-based load balancer (user enrolment and client app configuration)
* gateway1, gateway2, gateway-lb - two Defguard Gateway components with an Envoy-based load balancer (VPN gateways)

## Initial configuration

Notice that the Docker Compose file contains only minimal configuration parameters. This is one of the major changes in the new version. All configuration that was previously stored in environment variables or configuration files is now stored in the database and initialized using a convenient **setup wizard**.

To begin the initial configuration just visit this address http://localhost:8000/ after you started the stack with Docker Compose. Defguard will detect that this is fresh instance and will welcome you with the setup wizard.

The setup process contains several major steps:

* Creating admin user account
* Creating a custom CA for securing inter-component communication
* Setting up Edge component to securely expose selected functionality to the internet while keeping the Core component isolated
* Creating an initial VPN Location (logical VPN site)
* Creating a VPN Gateway for the initial VPN Location (actual VPN server)

For the stable version of 2.0 (or in following alpha) we'll also add a **migration wizard** that will help you upgrading from previous Defguard version to the latest one with ease.

### Example setup

{% stepper %}
{% step %}
#### Go to the Core Component UI

Visit [http://localhost:8000](http://localhost:8000/) after starting the stack using Docker Compose.  See the Initial Setup Wizard is being triggered automatically.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 22.29.27.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Create admin user account

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 22.29.56.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Provide general configuration

You can set `http://localhost:8080` as the _Public Edge Component URL_ if you'll only test the setup using the Defguard Desktop Application on the same machine. If you're planning to also test the setup using the Defguard Mobile Application connected to the same local network, setting it to the IP address you got in the network is a better idea. I got mine by executing this command on my MacOS:

```
ipconfig getifaddr en0
192.168.83.132
```

and set it to `http://192.168.83.132:8080`.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 22.30.27.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Create custom Certificate Authority

Previously Defguard administrators had to configure this manually and often skipped the process. We wanted to make this step much easier because we know it's crutial for the system's security. Now Defguard handles both creating the custom CA and using it to generate certificates for Edge and Gateway component while adopting them.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 22.30.42.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Create and adopt first Edge Component

If you've started the all the services from the provided Docker Compose configuration, the Edge Component service is already started and waiting to be adopted in Defguard Core.

Use the Docker service name as the _IP or Domain_ while configuring the component.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.07.31.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.07.33.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Create first Location

Here same as with Edge Component, set the Gateway public address to the IP you got in your local network.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.08.14.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.08.38.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.08.41.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.08.49.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.08.53.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.05.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Create first Gateway for the newly created Location

If you've started the all the services from the provided Docker Compose configuration, the Gateway Component service is already started and waiting to be adopted in Defguard Core.

Use the Docker service name as the _IP or Domain_ while configuring the component.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.22.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.24.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Inspect the newly create Edge Component, Location and Gateway Component

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.42.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.09.37.png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Enjoy fully redesigned interface

After finishing the initial setup Defguard is fully operational. You can manage your instance using the fully redesigned UI/UX.

You can also enrol users and connect to the newly crated Location.

You'll notice changes in every part of the interface but some areas changed in a very significant way. Check those modules for sure:

* VPN overview - strictly a dashboard for the administrator, previously mixed with system configuration, which was confusing. Also we've significatly reafactored our statistics module to make sure the dashboard is responsive even for large deployments.
* Dedicated Locations page - previously hidden somewhere in the VPN overview page, mixed with dashboard, now a clear Location listing and management.
* Firewall (formerly ACL) - new nomenclature (Aliases, Destinations, Rules), brand new Alias, Destination and Rule form. The Rule form, despite realising a complex task of creating a firewall rule, is intuitive and guides the user through the process.
* Settings - since all of the settings are now stored in the database, they can be managed with the UI. All system parameter got divided into logical sections, with broad descriptions, making it much easier to configure you system.
* Edge Components (formerly Proxy) page - brand new page for managing Edge Components (exposing selected Core functionality to the internet while keeping the Core isolated).

## Test the setup configured using the wizard

Now the only thing left to do is to test the setup. You can test the following scenario:

1. Create a new user in the .
2. Sign in as the user.
3. Add a TOTP MFA method in the user's profile.
4. Enrol as the new user using the Desktop or Mobile app.
5. Connect to the created Location.
6. See a new session visible in the VPN overview.

## High Availability of Edge and Gateway components

{% hint style="info" %}
This feature will be available only in the Enterprise plan after v2.0 reaches stable version.
{% endhint %}

Another major feature of v2.0 is High Availability in active-active mode for the Edge and Gateway components.

You can now add multiple Gateways to your Locations. Users will still connect to a single Gateway (using sticky sessions), but in the event of a Gateway failure, their VPN connection will remain active and be handled by another Gateway.

You can also add multiple Edge components to ensure that enrollment, configuration updates, and MFA session initiation are fast and fail-safe.

### Example setup

{% stepper %}
{% step %}
#### Add another Edge Component

If you've started the all the services from the provided Docker Compose configuration, the additional Edge Compoent service is already started and waiting to be adopted in Defguard Core. Both Edge Components are behind a basic NGINX-based load balancer.&#x20;

Use the Docker service name as the _IP or Domain_ while configuring the component.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.37.27.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.38.28.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.38.31.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.38.39.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
#### Add another Gateway Component to your Location

If you've started the all the services from the provided Docker Compose configuration, the additional Gateway Component service is already started and waiting to be adopted in Defguard Core. Both Gateway Components are behind a basic Envoy-based load balancer.&#x20;

Use the Docker service name as the _IP or Domain_ while configuring the component.

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.40.06.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.41.04.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.41.07.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2026-02-08 at 23.42.14.png" alt=""><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}

## Test the High Availability and Failover

### Test HA for Edge Components

1. Display logs of both Edge Components using `docker compose logs -f edge1 edge2`
2. Trigger enrolment or MFA VPN connection using the Defguard Desktop or Mobile Application.
3. Notice traffic being directed to both Edge Components using round robin strategy on the NGINX load balancer.
4. Stop one of the Edge Components using `docker compose stop edge1`&#x20;
5. Notice the enrolment process or MFA VPN connections working as expected on the Edge Components that's left.

### Test HA for Gateway Components

1. Display logs of both Gateway Components using `docker compose logs -f gateway1 gateway2`
2. Ping the VPN gateway to make sure that the connection if down using `ping 10.10.10.1`. Keep it running through the test.
3. Connect to the VPN Location.
4. Ping the VPN gateway to make sure that the connection if up.
5. Now play with stoping on of the gateways `docker compose stop gateway1`  or `docker compose stop gateway2` .  Make sure you don't stop both of them.
6. Notice that the VPN connection is alive the whole time.

