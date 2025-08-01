# REST API

{% hint style="warning" %}
This is an enterprise feature. To use it, purchase our [enterprise license](../enterprise/license.md) or ensure that your deployment does not exceed the [usage limits](../enterprise/license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

{% hint style="warning" %}
API functionality:

1. requires Defguard version 1.2.4+
2. is also **available without enterprise license**, if your instance does not exceed the limits [described here](../enterprise/license.md#enterprise-is-free-up-to-certain-limits).
{% endhint %}

## REST API documentation

You can explore the Defguard REST API using [Swagger UI](https://swagger.io/tools/swagger-ui/) by going to `<YOUR_DEFGUARD_URL>/api-docs`.

API specification JSON in OpenAPI format can also be fetched from `<YOUR_DEFGUARD_URL>/api/v1/api-docs`.

Admin users can generate API tokens to enable request authentication for custom external tools which use Defguard REST API.

Tokens retain the same access permissions as their owner, so be careful when sharing them with others.

## Generating API token

## Setup

To generate a new API token, go to your profile page and click the `Add new API Token`button:

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

Fill in your chosen token name and submit form:

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

Copy generated token. This is the only time the token will be available in plain text form. If you lose it you will have to generate a new one.

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

In the API token list you can later rename or delete a token:

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

## Usage

Defguard API uses a standard **Bearer token authentication** scheme.

This means that an API token can be passed in the `Authorization` header to authenticate a given request instead of a session cookie used by the web UI:

```bash
Authorization: Bearer <token>
```

Example GET request:

```bash
curl -H "Authorization: Bearer <token>" <YOUR_DEFGUARD_URL>/api/v1/me
```
