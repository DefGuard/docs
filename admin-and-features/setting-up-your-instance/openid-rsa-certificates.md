# OpenID RSA Certificates

By default Defguard uses [HMAC](https://en.wikipedia.org/wiki/HMAC) algorithm for OIDC token validation. If you want to use [RSA](https://en.wikipedia.org/wiki/RSA\_\(cryptosystem\)), you'll have to Generate RSA keys:

```
openssl genpkey -out .volumes/core/rsakey.pem -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

And then add environment variable to CORE: `DEFGUARD_OPENID_KEY: /path/to/rsa.pem`

For example if you use docker/compose based deployment, add the env variable and mount the volume in docker-compose core service:

```yaml
core:
  ...
  environment:
    ...
    DEFGUARD_OPENID_KEY: /keys/rsakey.pem
  ...
  volumes:
    - ./.volumes/core/rsakey.pem:/keys/rsakey.pem
```
