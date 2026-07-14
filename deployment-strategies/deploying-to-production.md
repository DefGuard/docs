# Deploying to Production

{% stepper %}
{% step %}
**Gain knowledge**

Before you start your deployment, take a moment to learn about Defguard.

The following articles will help you make deployment decisions and understand which features you may want to configure afterward. Make sure to read them carefully.

* [Defguard’s features](../about/features-overview.md)
* [Defguard’s architecture](../in-depth/architecture/)
{% endstep %}

{% step %}
**Choose your deployment strategy**

Decide which deployment approach best fits your infrastructure and security requirements. Choose from different [deployment strategies](overview.md#choose-your-deployment-strategy) and their recommended use cases.
{% endstep %}

{% step %}
**Prepare your environment**

Make sure your infrastructure meets all [system and network requirements](hardware-os-network-and-firewall-recommendations.md).
{% endstep %}

{% step %}
**Deploy using the chosen strategy**

Deploy your instance using the chosen [strategy](overview.md#choose-your-deployment-strategy). Make sure to follow the right [deployment sequence](overview.md#initial-deployment-sequence).
{% endstep %}

{% step %}
**Test if everything works as expected**

Follow our [guide](production-deployment-verification-guide.md) to test if your deployment is secure and works as expected.
{% endstep %}

{% step %}
**Configure features**

Follow detailed descriptions of [Defguard’s features](https://github.com/DefGuard/docs/blob/v1.6/deployment-strategies/broken-reference/README.md). As you follow along, you can adjust the configuration directly within your instance.

For a detailed list of all configurable things through environmental variables, options or configuration files follow [this reference](configuration.md).
{% endstep %}

{% step %}
**Your secure infrastructure is ready for use**

Optional but recommended additional steps:

* [Configuring backups](overview.md#backup)
* [Setting up for high availability and failover](high-availability-and-failover/)
{% endstep %}
{% endstepper %}
