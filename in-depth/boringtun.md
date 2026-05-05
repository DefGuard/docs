# BoringTun

BoringTun is an implementation of the [WireGuard® protocol](https://www.wireguard.com/protocol/) designed for portability and speed. It is written in [Rust](https://rust-lang.org/) and was originally published by Cloudflare.

In the Defguard ecosystem, BoringTun is an important building block for running WireGuard functionality in user space where a kernel implementation is unavailable, unsuitable, or not the preferred integration model. This makes it especially useful on platforms where portability and embedding matter.

The Defguard team uses a fork of the [original repository](https://github.com/cloudflare/boringtun) with the following changes:

* [Swift](https://www.swift.org/) bindings using [UniFFI](https://mozilla.github.io/uniffi-rs)
* cleaned-up code
* dependencies updated regularly

BoringTun is used by [wireguard-rs](https://github.com/defGuard/wireguard-rs), which serves as a foundation for Defguard Gateway, and by [Defguard Client](https://github.com/defGuard/client) for handling VPN tunnels on [macOS](https://www.apple.com/os/macos/).

In practice, this means BoringTun helps Defguard support WireGuard-based connectivity in environments where kernel-level WireGuard integration is not the default approach. It also allows the project to build platform-specific integrations on top of a shared Rust implementation.
