# Securing gRPC communication

There are two main gRPC communication endpoints:

1. defguard core has a gRPC port for communicating with gateways,
2. defguard proxy has a gRPC port for communicating with defguard core.

{% hint style="danger" %}
It is **critical** that:

1. defguard core gRPC port is open on firewall only for IPs of your gateway nodes.
2. defguard proxy gRPC port is open on firewall only for the IP address of defguard core server.
3. If you want an additional layer of security - create a **custom SSL CA** - and provide core, proxy & gateway certificates from that CA - so **any other connections to the gRPC services will not be accepted.**
4. Even if you have secured the network ports/firewall and do not want to create a custom SSL CA - please secure gRPC traffic with SSL and reverse-proxy.
{% endhint %}

## gRPC SSL using reverse-proxy

{% hint style="warning" %}
This type of SSL termination should be done only if you trust your network and have secured gRPC ports on firewall.
{% endhint %}

If core or proxy are using reverse proxy (NGINX, Caddy, Traefik, ...) that handles SSL termination (for [example in this tutorial we show how to configure gRPC SSL reverse proxy using NGINX](standalone-package-based-installation.md#nginx)), then only you need to configure CA certificate paths for:

* gateway - in gateway.toml add path to CA file, for example when using Let'sEncrypt you configure the CA path:

`grpc_ca = "/etc/letsencrypt/live/domain.name/chain.pem"`

* core - same way you need to configure PROXY CA File path:

`DEFGUARD_PROXY_GRPC_CA: /etc/letsencrypt/live/domain.name/chain.pem`

## gRPC SSL manual&#x20;

To enable secure gRPC communication between all components, you'll need:

* CA certificate  that wil be used to generate client certificates and also configured in:
  * core: `DEFGUARD_PROXY_GRPC_CA`  and path to CA file
  * gateway (gateway.toml config): `grpc_ca = "/path/to/ca.pem"`
* certificates for CORE and Proxy that needs to be configured in the `DEFGUARD_GRPC_CERT=/path/to/cert and DEFGUARD_PROXY_GRPC_CERT=/path/to/cert`
* private keys for the certificates: `DEFGUARD_GRPC_KEY: /ssl/defguard-grpc.key and DEFGUARD_PROXY_GRPC_KEY=/path/to/cert`

[Here](https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/) is a good tutorial on how to generate a self-signed certificate.

Put the certificates in .volumes/ssl directory if you are using our docker-compose base deployment.

##

