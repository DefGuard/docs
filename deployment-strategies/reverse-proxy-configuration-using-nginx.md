# Reverse Proxy configuration using NGINX

## Introduction

This guide explains how to configure [NGINX](https://nginx.org/) as a reverse proxy for Defguard's components (Core and Proxy). The reverse proxy acts as an intermediary between users and Defguard services, handling HTTPS requests, routing internal gRPC communication, and ensuring encrypted connections between all components.

To provide HTTPS encryption, this guide also uses [Certbot](https://certbot.eff.org/), a free, open-source tool from the [Let’s Encrypt](https://letsencrypt.org/) project. Certbot automatically issues and renews SSL/TLS certificates, allowing you to secure your Defguard domains without manual certificate management.

### Installing NGINX and Certbot

To install and prepare NGINX with Let’s Encrypt certificates:

```bash
apt install nginx certbot
systemctl enable nginx.service
systemctl start nginx.service
```

Disable the default configuration to avoid conflicts:

```bash
unlink /etc/nginx/sites-enabled/default
```

### Obtaining SSL Certificates

Before configuring NGINX, issue valid SSL certificates for your domains.\
In this example we use:

* Core: **my-server.defguard.net**
* Enrollment (Proxy): **enroll.defguard.net**

Generate certificates with Certbot:

```bash
certbot certonly \
    --non-interactive \
    --agree-tos \
    --standalone \
    --email admin@yourdomain.com \
    -d my-server.defguard.net \
    -d enroll.defguard.net
```

Certbot will generate certificate in fullchain.pem and privkey.pem in the following paths:

```
/etc/letsencrypt/live/my-server.defguard.net
/etc/letsencrypt/live/enroll.defguard.net
```

### Defguard Core NGINX configuration

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
    listen 443 ssl;
    http2 on;
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

Enable the configuration and reload NGINX:

```bash
ln -s /etc/nginx/sites-available/my-server.defguard.net.conf /etc/nginx/sites-enabled/my-server.defguard.net.conf

systemctl reload nginx.service
```

To verify, run:

```bash
curl https://my-server.defguard.net/api/v1/health
# Expected output:
alive
```

{% hint style="info" %}
If you use this simple setup and run all services on one server, you can use [NGINX access restrictions](https://docs.nginx.com/nginx/admin-guide/security-controls/controlling-access-proxied-tcp/) for securing core and allowing to access the _my-server.defguard.net_ only to selected networks - blocking the direct access from the Internet.
{% endhint %}

### Defguard Proxy (Enrollment Service) NGINX configuration

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

### Security Recommendations

* Only expose **HTTPS ports (443)** for web access.
* Do **not** expose internal **gRPC ports** (444, 50051, 50055) directly to the Internet.

### Summary

After completing the configuration:

* Defguard Core is available at `https://my-server.defguard.net`
* Enrollment and onboarding services are available at `https://enroll.defguard.net`
* Both services are secured with SSL and reverse-proxied through NGINX.
