---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/using-a-userspace-wireguard-implementation
---

# Using a userspace WireGuard implementation

Defguard Gateway supports a userspace WireGuard implementation based on BoringTun. BoringTun is Cloudflare’s userspace implementation of the WireGuard VPN protocol, written in Rust with a focus on portability and speed. Defguard uses its own fork of [BoringTun](https://github.com/defGuard/boringtun), which is publicly available.

Using a userspace WireGuard implementation is useful on platforms where native kernel support is unavailable, limited, or impractical to use. It can also help in environments where running WireGuard entirely in user space better matches the operating model of the platform.

On platforms where native WireGuard support exists, such as Linux or FreeBSD, using the kernel implementation is generally recommended. The kernel implementation usually provides better performance, lower overhead, and behaviour that is closer to the standard WireGuard deployment model.

You can enable the userspace implementation in one of the following ways:

* by setting the `userspace` configuration option,
* by passing the `--userspace` argument when launching the Gateway binary,
* by setting the `DEFGUARD_USERSPACE=1` environment variable.

Make sure you enable only one configuration method in a way that matches how you deploy Gateway. For example, if you manage Gateway through a configuration file, prefer setting the option there. If you launch the binary manually or through a custom service wrapper, the command-line argument or environment variable may be more convenient.

After enabling userspace mode, verify that the gateway starts correctly and that the WireGuard interface behaves as expected in your environment. Because userspace operation differs from the kernel-based model, it is a good idea to validate connectivity, routing, and overall performance before using it in production.
