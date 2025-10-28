# Using RSA instead of HMAC for OpenID key

By default, Defguard uses [HMAC](https://en.wikipedia.org/wiki/HMAC) algorithm for OIDC token validation and the. If you want to use [RSA](https://en.wikipedia.org/wiki/RSA_\(cryptosystem\)), you'll have to configure the Defguard core `DEFGUARD_OPENID_KEY` configuration variable with the path to the RSA private key.

You can generate the RSA key with:

```
openssl genpkey -out /path/to/rsakey.pem -algorithm RSA -pkeyopt rsa_keygen_bits:4096
```
