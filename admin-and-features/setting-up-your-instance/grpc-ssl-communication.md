---
icon: light-emergency-on
---

# Securing gRPC communication

Defguard Core has two main communication endpoints:

1. gRPC port for communicating with Defguard Gateways,
2. gRPC port for communicating with Defguard Core.

{% hint style="danger" %}
It is **critical** that:

1. Defguard Core's gRPC port is open on a firewall only for IP addresses of Defguard Gateway nodes.
2. Defguard Proxy's gRPC port is open on a firewall only for the IP address of Defguard Core.
3. If you want an additional layer of security, then you should create a **custom SSL Certificate Authority (CA)**, and provide Core, Proxy and Gateway Certificates from that CA so **any other connections to the gRPC services will not be accepted.**
4. Even if you have secured the network ports/firewall and do not want to create a custom SSL CA, please secure gRPC traffic with SSL and a reverse proxy.
{% endhint %}

## Custom SSL CA & certificates

To secure not only with firewall communication between all Defguard gRPC components, a custom SSL chain of certificates should be used. This way the trust will be ensured on the Transport Layer Security (TLS) level.

It is important to embed a correct domain name into the certificate as _X509v3 Subject Alternative Name_. The domain name must match the one under which a service is being hosted.

### Quick setup

To quickly generate a set of SSL certificates using [OpenSSL](https://openssl-library.org) or [LibreSSL](https://www.libressl.org), use the following:

* Generate Certificate Authority (CA) cerfiticate and key for domain _example.local_

```sh
openssl req -x509 -noenc -subj '/CN=example.local' -newkey rsa:4096 -keyout ca.key -out ca.crt
```

* Generate private key and Certificate Signing Request (CSR)

```sh
openssl req -noenc -newkey rsa:4096 -keyout core.key -out core.csr -subj '/CN=example.local' -addext subjectAltName=DNS:example.local
```

* Generate certificate by signing the CSR, valid for 365 days

```sh
openssl x509 -req -in core.csr -CA ca.crt -CAkey ca.key -days 365 -out client.crt -copy_extensions copy
```

{% hint style="info" %}
Repeat the last two steps for other services (e.g. change core.csr, core.crt, and core.key to gateway.csr, gateway.crt, gateway.key), just change the domain name accordingly.
{% endhint %}

To display certificate file contents:

```sh
openssl x509 -noout -text -in core.crt
```

### Defguard configuration

* Certificate Authority (CA) certificate (usually used to generate custom certificates) configured in:
  * core: `DEFGUARD_PROXY_GRPC_CA`  and path to CA file
  * gateway (gateway.toml config): `grpc_ca = "/path/to/ca.pem"`
* certificates for Defguard Core and Defguard Proxy that needs to be configured in the
  * `DEFGUARD_GRPC_CERT=/path/to/cert`&#x20;
  * `DEFGUARD_PROXY_GRPC_CERT=/path/to/cert`
  * private keys for the certificates:
    * `DEFGUARD_GRPC_KEY: /ssl/defguard-grpc.key`
    * `DEFGUARD_PROXY_GRPC_KEY=/path/to/cert`

[Here](https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/) is a good tutorial on how to generate a self-signed certificate.

## Trusted CA (eg. Let'sEncrypt or others)

Often (like in the standalone package based installation tutorial) gRPC communication can be secured by a reverse proxy (NGINX, Caddy, Traefik, etc.) that handles SSL termination. It's common to use typical trusted CA (that is used for typical HTTPS traffic) like Let'sEncrypt or others.

{% hint style="danger" %}
While this secures the transport layer and encrypts communication between defguard components - it's does not provide authorization between components like Custom CA does.

Thus, this type of SSL termination should only be done if you trust your network and have secured gRPC ports on firewall.
{% endhint %}

If Defguard Core or Defguard Proxy are using reverse proxy with SSL termination, then only you need to configure CA certificate paths for:

* Defguard Gateway – in _gateway.toml_ add path to CA certificate file (in PEM format); for example when using standard Let'sEncrypt installation ([Certbot](https://certbot.eff.org)), you configure the CA path like this:
  * `grpc_ca = "/etc/letsencrypt/live/domain.name/chain.pem"`
* Defguard Core – similarily, you need to configure Proxy CA certificate file using **DEFGUARD\_PROXY\_GRPC\_CA** environment variable:
  * `DEFGUARD_PROXY_GRPC_CA: /etc/letsencrypt/live/domain.name/chain.pem`

