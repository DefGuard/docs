# Trusting an internal CA for an OIDC provider in Kubernetes

If your OIDC provider uses an HTTPS certificate signed by an internal CA, Defguard Core must trust that CA. Otherwise, backend requests such as OIDC discovery or exchanging an authorization code for tokens may fail with TLS certificate verification errors, even when browser-based authentication works.

The Helm chart does not currently provide dedicated options for installing a custom CA bundle. Use one of the following workarounds.

**Option 1: Build a custom Core image**

Add your PEM-encoded CA certificate to the system trust store:

```dockerfile
  FROM ghcr.io/defguard/defguard:<core-version>

  COPY internal-root-ca.crt /usr/local/share/ca-certificates/internal-root-ca.crt
  RUN update-ca-certificates
```

Push the image to your registry and select it in the chart values:

```yaml
  image:
    repository: registry.example.com/defguard-with-internal-ca
    tag: "<your-image-tag>"
```

This preserves the image's existing trusted public CAs.

**Option 2: Mount a CA bundle and set SSL\_CERT\_FILE**

If your CA bundle is already available in a ConfigMap, patch the Core Deployment to mount it and point SSL\_CERT\_FILE to the mounted file:

```yaml
  spec:
    template:
      spec:
        volumes:
          - name: ca-bundle
            configMap:
              name: ca-bundle
        containers:
          - name: defguard
            env:
              - name: SSL_CERT_FILE
                value: /etc/ssl/certs/ca-bundle.crt
            volumeMounts:
              - name: ca-bundle
                mountPath: /etc/ssl/certs/ca-bundle.crt
                subPath: ca-bundle.crt
                readOnly: true
```

Adjust the Deployment container name to match your installation. The ConfigMap must exist in the same namespace and contain a ca-bundle.crt key with the PEM-encoded bundle.

Mounting the file alone does not install it into the system trust store. SSL\_CERT\_FILE tells the TLS library where to find it.

Keep the patch in your deployment automation so Helm upgrades preserve it.
