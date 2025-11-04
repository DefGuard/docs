# Reverse Proxy configuration using Nginx

## Introduction

This guide explains how to configure [NGINX](https://nginx.org/) as a reverse proxy for Defguard's components (Core and Proxy). The reverse proxy acts as an intermediary between users and Defguard services, handling HTTPS requests, routing internal gRPC communication, and ensuring encrypted connections between all components.

To provide HTTPS encryption, this guide also uses [Certbot](https://certbot.eff.org/), a free, open-source tool from the [Let’s Encrypt](https://letsencrypt.org/) project. Certbot automatically issues and renews SSL/TLS certificates, allowing you to secure your Defguard domains without manual certificate management.

{% hint style="info" %}
We assume in this guide that you run your Core and Proxy services on separate servers, and you run Certbot and Nginx on each one of them.
{% endhint %}

## Installing Nginx and Certbot

Run the followign command to install Nginx and Certbot.

```bash
apt install nginx certbot
```

Disable the default Nginx configuration to avoid conflicts:

```bash
unlink /etc/nginx/sites-enabled/default
```

## Obtaining SSL certificates

Use Certbot to generate SSL certificates.&#x20;

For each service (Core, Proxy), run the following command on the server that your domain’s DNS records resolve to. Ensure that inbound traffic on port 80 is allowed by the firewall and that no other process is using this port.

{% hint style="info" %}
Certbot verifies domain ownership using the HTTP-01 challenge, where it temporarily serves a validation file over port 80 for the exact domain you are requesting a certificate for.&#x20;

If the request fails with a timeout or connection error, Let’s Encrypt could not reach this temporary server. This usually means the DNS record for that domain does not point to the correct public IP of the machine running Certbot, port 80 is blocked (firewall or Security Group), an IPv6 (AAAA) record is published but not supported on the server, or another service is already using port 80.&#x20;

Ensure the domain’s DNS resolves to this server’s public IP, inbound port 80 is open, and no other service is binding the port before trying again.
{% endhint %}

### Obtaining SSL certificate for Core service

Use the following command to generate certificate with Certbot. Replace the example domain for the Core service (my-server.defguard.net) with your own.

```bash
certbot certonly \
    --non-interactive \
    --agree-tos \
    --standalone \
    --email admin@yourdomain.com \
    -d my-server.defguard.net
```

Certbot will generate certificate in fullchain.pem and privkey.pem in the following path:

```
/etc/letsencrypt/live/my-server.defguard.net
```

### Obtaining SSL certificate for Proxy service

Use the following command to generate certificate with Certbot. Replace the example domain for the Proxy service (enroll.defguard.net) with your own.

```bash
certbot certonly \
    --non-interactive \
    --agree-tos \
    --standalone \
    --email admin@yourdomain.com \
    -d enroll.defguard.net
```

Certbot will generate certificate in fullchain.pem and privkey.pem in the following path:

```
/etc/letsencrypt/live/enroll.defguard.net
```

## Configuring and starting Nginx

### Configuring and starting Nginx for Core service

Create a new configuration file for the Core service:

`/etc/nginx/sites-available/my-server.defguard.net.conf`

```nginx
upstream defguard {
    server 127.0.0.1:8000;
}

upstream defguard-grpc {
    server 127.0.0.1:50055;
}

server {
    listen 443 ssl http2;
    server_name my-server.defguard.net;
    access_log /var/log/nginx/defguard.log;
    error_log /var/log/nginx/defguard.e.log;

    ssl_certificate /etc/letsencrypt/live/my-server.defguard.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/my-server.defguard.net/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/my-server.defguard.net/fullchain.pem;

    client_max_body_size 128M;

    location / {
        proxy_pass          http://defguard;
        proxy_set_header    Host                $host;
        proxy_set_header    X-Real-IP           $remote_addr;
        proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
        proxy_http_version  1.1;
        proxy_set_header    Upgrade             $http_upgrade;
        proxy_set_header    Connection          "upgrade";
    }
}

server {
    listen 444 ssl http2;
    server_name my-server.defguard.net;
    access_log /var/log/nginx/defguard-grpc.log;
    error_log /var/log/nginx/defguard-grpc.e.log;

    ssl_certificate /etc/letsencrypt/live/my-server.defguard.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/my-server.defguard.net/privkey.pem;

    client_max_body_size 200m;

    location / {
        grpc_pass grpc://defguard-grpc;
    }
}
```

Enable the configuration and start Nginx:

<pre class="language-bash"><code class="lang-bash"><strong>ln -s /etc/nginx/sites-available/my-server.defguard.net.conf /etc/nginx/sites-enabled/my-server.defguard.net.conf
</strong>
systemctl start nginx.service
</code></pre>

To verify, run:

```bash
curl https://my-server.defguard.net/api/v1/health
# Expected output:
alive
```

{% hint style="info" %}
If you use this simple setup and run all services on one server, you can use [NGINX access restrictions](https://docs.nginx.com/nginx/admin-guide/security-controls/controlling-access-proxied-tcp/) for securing core and allowing to access the _my-server.defguard.net_ only to selected networks - blocking the direct access from the Internet.
{% endhint %}

### Configuring and starting Nginx for Proxy service

The Proxy service exposes APIs for enrollment, remote onboarding, and desktop client configuration.\
Create its NGINX configuration file:

`/etc/nginx/sites-available/enroll.defguard.net.conf`

```nginx
upstream defguard-proxy {
    server 127.0.0.1:8080;
}

upstream proxy-grpc {
    server 127.0.0.1:50051;
}

server {
    listen 443 ssl http2;
    server_name enroll.defguard.net;
    access_log /var/log/nginx/enroll.log;
    error_log /var/log/nginx/enroll.e.log;

    ssl_certificate /etc/letsencrypt/live/enroll.defguard.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/enroll.defguard.net/privkey.pem;

    client_max_body_size 200m;

    location / {
        proxy_http_version 1.1;
        proxy_pass         http://defguard-proxy;
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection "upgrade";
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_read_timeout 86400s;
        proxy_send_timeout 86400s;
    }
}

server {
    listen 444 ssl http2;
    server_name enroll.defguard.net;
    access_log /var/log/nginx/enroll-grpc.log;
    error_log /var/log/nginx/enroll-grpc.e.log;

    ssl_certificate /etc/letsencrypt/live/enroll.defguard.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/enroll.defguard.net/privkey.pem;

    client_max_body_size 200m;

    location / {
        grpc_pass grpc://proxy-grpc;
        grpc_socket_keepalive on;
        grpc_read_timeout 3000s;
        grpc_send_timeout 3000s;
        grpc_next_upstream_timeout 0;

        proxy_request_buffering off;
        proxy_buffering off;
        proxy_connect_timeout 3000s;
        proxy_send_timeout 3000s;
        proxy_read_timeout 3000s;
        proxy_socket_keepalive on;

        keepalive_timeout 90s;
        send_timeout 90s;

        client_body_timeout 3000s;
    }
}
```

Enable and restart NGINX:

```bash
ln -s /etc/nginx/sites-available/enroll.defguard.net.conf /etc/nginx/sites-enabled/enroll.defguard.net.conf

systemctl restart nginx.service
```

## Security Recommendations

* Only expose **HTTPS ports (443)** for web access.
* Do **not** expose internal **gRPC ports** (444, 50051, 50055) directly to the Internet.

## Summary

After completing the configuration:

* Defguard Core is available at `https://my-server.defguard.net`
* Enrollment and onboarding services are available at `https://enroll.defguard.net`
* Both services are secured with SSL and reverse-proxied through NGINX.
