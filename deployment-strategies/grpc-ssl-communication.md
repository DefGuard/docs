# Securing gRPC communication

Defguard components exchange data over gRPC, which must be properly secured to protect sensitive information and prevent unauthorized access.

## Firewall rules

Defguard components expose two ports that require firewall-level protection:

* Defguard Core exposes a gRPC port for communication with Defguard Gateways.
* Defguard Proxy exposes a gRPC port for communication with Defguard Core.

Limit access to gRPC ports:

* Allow Core’s gRPC port only from Gateway IPs.
* Allow Proxy’s gRPC port only from Core’s IP.

## SSL encryption

Even if you already use [SSL on a reverse proxy](reverse-proxy-configuration-using-nginx.md#obtaining-ssl-certificates), this only protects external traffic. Internal gRPC connections between Proxy, Core, and Gateways occur behind the proxy and must also be encrypted and authenticated. These connections carry sensitive operational data and should never be left unprotected.

You can **choose one** of two approaches:

* [Trusted CA certificates](grpc-ssl-communication.md#trusted-ca-certificates) (encryption only) - use certificates issued by a recognized Certificate Authority (e.g., Let’s Encrypt). This approach provides encrypted traffic.
* [Custom internal CA](grpc-ssl-communication.md#custom-internal-ca) (encryption + authentication) - create your own Certificate Authority and issue certificates for Core, Proxy, and Gateway. This setup enables mutual TLS (mTLS), meaning each component both encrypts and authenticates the connection - ensuring that only trusted Defguard services can communicate with each other.

Choose one of these options based on your environment: trusted CA for simplicity, or a custom CA for full Zero Trust mutual authentication.

### Trusted CA certificates

If you followed our [guide on configuring SSL for reverse proxy](reverse-proxy-configuration-using-nginx.md#obtaining-ssl-certificates) your certificates should be located in the following path `/etc/letsencrypt/live/domain.name/`. Use the PEM-formatted CA certificate for configuring Defguard components.          &#x20;

{% hint style="warning" %}
While this secures the transport layer and encrypts communication between Defguard components - it does not provide authorization between gRPC components like [Custom internal CA](grpc-ssl-communication.md#custom-internal-ca) does.&#x20;

Thus, this type of SSL termination should only be done if you trust your network and have secured gRPC ports on firewall.
{% endhint %}

#### Configure Defguard Core

Add path to CA certificate file using command line arguments:

```bash
defguard --proxy-grpc-ca /etc/letsencrypt/live/domain.name/chain.pem
```

or using the service's configuration file:

```toml
proxy_grpc_ca = "/etc/letsencrypt/live/domain.name/chain.pem"
```

or using environment variable:

```bash
env DEFGUARD_PROXY_GRPC_CA=/etc/letsencrypt/live/domain.name/chain.pem \
    defguard
```

#### Configure Defguard Gateway

Add path to CA certificate file using command line arguments:

```bash
defguard-gateway --grpc-ca /etc/letsencrypt/live/domain.name/chain.pem
```

or using the service's configuration file:

```toml
grpc_ca = "/etc/letsencrypt/live/domain.name/chain.pem"
```

or using environment variable:

```bash
env DEFGUARD_GRPC_CA=/etc/letsencrypt/live/domain.name/chain.pem \
    defguard-gateway
```

### Custom internal CA

{% hint style="warning" %}
It is important to embed a correct domain name into the certificate as _X509v3 Subject Alternative Name_. The domain name must match the one **under which a service is being hosted**.
{% endhint %}

#### Generate certificates

To quickly generate a set of SSL certificates using [OpenSSL](https://openssl-library.org) or [LibreSSL](https://www.libressl.org), use the following:

* Generate Certificate Authority (CA) certificate and key for domain _example.local_

```sh
openssl req -x509 -noenc -subj '/CN=example.local' -newkey rsa:4096 -keyout ca.key -out ca.crt
```

* Generate private key and Certificate Signing Request (CSR)

```sh
openssl req -noenc -newkey rsa:4096 -keyout core.key -out core.csr -subj '/CN=example.local' -addext subjectAltName=DNS:example.local
```

* Generate certificate by signing the CSR, valid for 365 days

```sh
openssl x509 -req -in core.csr -CA ca.crt -CAkey ca.key -days 365 -out core.crt -copy_extensions copy
```

{% hint style="info" %}
Repeat the last two steps for other services (e.g. change core.csr, core.crt, and core.key to gateway.csr, gateway.crt, gateway.key), just change the domain name accordingly.
{% endhint %}

To display certificate file contents:

```sh
openssl x509 -noout -text -in core.crt
```

#### Configure Defguard Core

Add paths to certificate files using command line arguments:

```sh
defguard --grpc-cert path/to/core.crt \
         --grpc-key path/to/core.key \
         --proxy-grpc-ca path/to/ca.crt
```

or using the service's configuration file:

```toml
grpc_cert = "path/to/core.crt"
grpc_key = "path/to/core.key"
proxy_grpc_ca = "path/to/ca.crt"
```

or using environment variables:

```sh
env DEFGUARD_GRPC_CERT=path/to/core.crt \
    DEFGUARD_GRPC_KEY=path/to/core.key \
    DEFGUARD_PROXY_GRPC_CA=path/to/ca.crt \
    defguard
```

#### Configure Defguard Proxy

Add paths to certificate files using command line arguments:

```sh
defguard-proxy --grpc-cert path/to/proxy.crt \
               --grpc-key path/to/proxy.key
```

or using the service's configuration file:

```toml
grpc_cert = "path/to/core.crt"
grpc_key = "path/to/core.key"
```

or using environment variables:

```sh
env DEFGUARD_PROXY_GRPC_CERT=path/to/proxy.crt \
    DEFGUARD_PROXY_GRPC_KEY=path/to/proxy.key
    defguard-proxy
```

#### Configure Defguard Gateway

Add paths to certificate files using command line arguments:

```sh
defguard-gateway --grpc-ca path/to/ca.crt
```

or using the service's configuration file:

```toml
grpc_ca = "path/to/ca.crt"
```

or using environment variables:

```sh
env DEFGUARD_GRPC_CA=path/to/ca.crt \
    defguard-gateway
```



