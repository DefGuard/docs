---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/using-a-userspace-wireguard-go-implementation
---

# Using a userspace wireguard-go implementation

Gateway currently supports using `wireguard-go`, a userspace WireGuard implementation. This approach is **not recommended** on platforms where a native support exists (e.g. Linux).

You can enable the userspace implementation by setting the `userspace` config option or a corresponding `DEFGUARD_USERSPACE` environment variable to `true`.

Because `wireguard-go` is not bundled by default with Defguard, it must be installed separately. The `wireguard-go` binary/command must be available on the host machine for it to function properly. On Docker, this currently requires building a custom image, as the base gateway images also don't come with `wireguard-go` pre-installed. This can be achieved as follows:

```docker
FROM golang:1.24.6-alpine AS builder
RUN apk add --no-cache git make

RUN git clone https://git.zx2c4.com/wireguard-go /src/wireguard-go \
 && cd /src/wireguard-go \
 && make

# Specify the desired Gateway's version here
FROM ghcr.io/defguard/gateway:latest

COPY --from=builder /src/wireguard-go/wireguard-go /usr/local/bin/wireguard-go

RUN chmod +x /usr/local/bin/wireguard-go
```

Note that when running the Docker container with a userspace implementation on a Linux host, the container requires a `NET_ADMIN` capability and access to `/dev/net/tun`, this can be set in a Docker compose:

```yaml
# Docker compose
    cap_add:
      - NET_ADMIN
    devices:
      - /dev/net/tun
```

Or via the command line:

```sh
docker run --cap-add=NET_ADMIN --device=/dev/net/tun [...]
```
