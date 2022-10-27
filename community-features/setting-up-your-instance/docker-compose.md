# Deploy Defguard with docker compose

We prepared a [git repository](https://github.com/DefGuard/deployment) with docker-compose configuration, clone it:

```
git clone git@github.com:DefGuard/deployment.git && cd deployment
```

In docker-compose directory you'll find a template env file called `.env.template`. Copy it:

```
cd docker-compose
cp .env.template .env
```

And then edit the values in `.env` file to setup your secrets. Those should be kept... well, secret.

Once that's done you can start the stack with:

```
docker-compose up
```

> Make sure you have [Docker](https://www.docker.com/get-started/) and [Docker Compose](https://docs.docker.com/compose/install/) installed.

That's it, Defguard should be running on port 80 of your server ([http://localhost](http://localhost) if you're running locally).
