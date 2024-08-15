# Docker Compose

If you want to customize your Docker installation manually, we prepared a [git repository](https://github.com/DefGuard/deployment) with a base docker-compose configuration. Clone it:

```
git clone https://github.com/DefGuard/deployment.git && cd deployment
```

In docker-compose directory you'll find a template env file called `.env.template`. Copy it:

```
cd docker-compose
cp .env.template .env
```

And then edit the values in `.env` file to setup your secrets. Those should be kept... well, secret. For more info on available configuration options see [configuration.md](configuration.md "mention").

{% hint style="info" %}
You can generate random strings for secrets with e.g.:

`openssl rand -base64 55 | tr -d "=+/" | tr -d '\n' | cut -c1-63`
{% endhint %}

## LDAP

To setup LDAP integration, you'll have to configure environment variables for core service. You'll find more info on how to do this [here](../ldap-synchronization-setup/).
