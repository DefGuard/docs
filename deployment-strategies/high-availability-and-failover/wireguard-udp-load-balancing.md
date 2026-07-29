# WireGuard UDP load-balancing

## The nature of WireGuard (UDP + stateful crypto)

WireGuard operates over UDP and establishes stateful cryptographic sessions between peers ([https://www.wireguard.com/protocol/](https://www.wireguard.com/protocol/)).

Key properties relevant to HA:

* WireGuard uses UDP (connectionless transport).
* Session keys are negotiated via handshake and stored in memory.
* Transport packets are encrypted using per-session symmetric keys.
* A transport packet can only be decrypted by the instance that holds the active session keys.

### What this means for HA

If a client completes a handshake with Gateway A:

* Only Gateway A can decrypt subsequent packets.
* If packets are routed to Gateway B, they will be dropped.
* Recovery requires the client to initiate a new handshake.

Therefore, simple round-robin load balancing is not sufficient.

You need:

* Health-aware load balancing
* Deterministic upstream selection (sticky routing)
* Fast backend ejection on failure

## Why a load balancer with health checks is required

WireGuard uses **UDP**, which is connectionless and provides no built-in failure detection. As a result:

* UDP has no connection state, acknowledgements, or reset signals.
* If a gateway crashes, the load balancer does not automatically detect it.
* The load balancer may continue forwarding traffic to a dead gateway.
* This results in silent packet drops and delayed failover.

To prevent this, a layer 4 load balancer must:

* Perform **active health checks** against each gateway.
* Immediately mark failed gateways as unhealthy.
* Re-route traffic to healthy instances.

Without properly configured health checks, high availability cannot be reliably achieved in a multi-gateway WireGuard setup.

## Why sticky sessions are mandatory

WireGuard sessions are bound to a specific gateway instance.

Once a client completes a handshake:

* Subsequent transport packets must reach the same gateway.
* If packets are distributed per-packet or per-datagram, decryption will fail.
* This results in silent packet drops.

Therefore, the load balancer must use deterministic routing:

* Hash-based routing (e.g., source IP hashing)
* Ring-hash or consistent-hash load balancing
* Never per-packet load balancing

Sticky routing ensures:

* All packets from a client reach the same gateway
* Failover only occurs when the backend is unhealthy

## Recommended load balancer: Envoy

We recommend [Envoy](https://www.envoyproxy.io/) for UDP load balancing.

Reasons:

* Native UDP proxy support
* Health checks with fine-grained timing controls
* Proper backend ejection on failure
* Production-grade L4 behaviour

Recommended configuration characteristics:

* lb\_policy: RING\_HASH
* hash\_policy: source\_ip
* Aggressive health check intervals
* Low fail thresholds for immediate failover

Example configuration can be found [here](https://github.com/DefGuard/deployment/tree/main/docker-compose2.0).

Envoy ensures:

* Traffic is consistently routed to one backend
* Dead backends are removed quickly
* Failover happens reasonably fast

## Operational gotchas and lessons learned

During implementation and testing, several subtle issues were discovered.

This section documents them to prevent future confusion.

#### Envoy health check timing is state-dependent

Envoy has multiple health-check timing parameters:

* interval
* no\_traffic\_interval
* no\_traffic\_healthy\_interval
* unhealthy\_interval
* healthy\_edge\_interval
* unhealthy\_edge\_interval

If only `interval` is configured, the effective behaviour may differ depending on traffic state.

Symptoms:

* Backend appears healthy even when container is dead.
* Failover occurs only after long delays.

**Solution: Explicitly configure all relevant health-check intervals to the same low value.**

#### NGINX is not suitable for this use case

NGINX stream/UDP proxy:

* Does not implement an active health-check mechanism, and therefore cannot reliably detect UDP backend failure.
* Does not reassign upstreams without hard socket errors.

This results in traffic never being routed to a healthy backend after a failure.

#### WireGuard may require a keepalive interval to fully recover after failover

Even after the load balancer successfully redirects traffic to a healthy gateway, the tunnel may not resume immediately.

Key points:

* When a gateway fails, the existing WireGuard session becomes invalid.
* The new gateway cannot decrypt transport packets from the old session and drops them silently.
* A new handshake must be initiated by the client.
* WireGuard only attempts a new handshake when triggered by traffic or by the **PersistentKeepalive** interval.

As a result:

* Tunnel recovery may take up to the configured keepalive interval.
* Shorter keepalive intervals result in faster failover recovery.

#### Expected failover behaviour

With proper configuration:

1. Gateway A crashes.
2. The load balancer health check marks it unhealthy within the configured health check interval.
3. Traffic is immediately routed to Gateway B.
4. Client initiates new WireGuard handshake.
5. Tunnel resumes operation.

Typical recovery time: 3-30 seconds, depending on Envoy interval settings and the client keepalive interval.

## Summary

High availability for WireGuard gateways requires:

* Layer 4 UDP load balancing
* Sticky sessions (consistent hashing)
* Aggressive health checks
* Correct dataplane-aware health endpoint
* Envoy with ring hash and properly configured health checks is the recommended solution.

The key principle is:

> WireGuard sessions are stateful and bound to a specific instance. HA must respect that constraint.

With correct configuration, gateway failover can be fast and reliable.
