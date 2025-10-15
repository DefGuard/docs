# Architecture

## Design Principles

Defguard is a zero-trust, VPN-centric access platform built on WireGuard®. Its design cleanly separates the control plane (Core) from the data plane (Gateways) and exposes a minimal public surface (Gateway for VPN; Proxy for secure web flows). Administrative and identity-sensitive services remain off the public internet, behind controlled network boundaries.

<figure><img src="../../.gitbook/assets/Untitled Diagram.drawio.png" alt=""><figcaption></figcaption></figure>

### **Separation of Concerns**

Each Defguard component serves a distinct purpose, ensuring clear functional isolation:

* The Core operates as the _control plane_: storing state, enforcing policy, and managing users and devices.
* The Gateway serves as the _data plane_: forwarding traffic, enforcing ACLs, and maintaining local operational independence.
* The Proxy acts as a _secure edge layer_: handling user-facing traffic and offloading authentication flows.

This modular architecture simplifies scaling, security audits, and upgrades.

### **Minimize Exposure**

Defguard is designed around the principle of least exposure — only the absolutely necessary components are reachable from the public internet.

* The Proxy is the _only_ component exposing a public HTTPS interface.
* The Gateway exposes only a single UDP port for WireGuard connections.
* The Core, database, and identity integrations (OIDC, LDAP, etc.) remain entirely private, accessible only from trusted networks.

This ensures the smallest possible attack surface while still supporting remote enrollment, authentication, and VPN connectivity.

### **Defense in Depth**

Every communication layer is protected by redundant and complementary security mechanisms:

* All internal API traffic uses gRPC (recommended over TLS).
* Firewall rules restrict network flows to specific IPs and ports.
* Sensitive services (Core, DB) are deployed in private network segments inaccessible from the internet.

This layered approach reduces the blast radius of any potential compromise.

### **Zero-Trust Posture**

Defguard adopts a zero-trust philosophy: no implicit trust is given to users, devices, or networks.

* Access is always authenticated and authorized dynamically.
* Multi-Factor Authentication (MFA) is supported natively for VPN connections via per-location pre-shared keys (PSKs) that serve as one-time authorization tokens.
* Device enrollment and configuration are bound to verified identities and can be revoked or rotated at any time.

This model ensures that even within an established tunnel, every access decision remains policy-driven and verifiable.

### **Graceful Degradation**

The platform is built for resilience and autonomy:

* Gateways cache configuration and continue to operate even if the Core becomes temporarily unavailable.
* Core services remain functional (e.g., OpenID login, admin operations) if a Gateway is offline.
* Gateways report state deltas to the Core when connectivity is restored, ensuring accurate statistics and consistency.

This approach prevents downtime during transient network or control-plane failures.

### **Observability and Auditability**

Security and reliability rely on visibility. Defguard provides built-in observability and audit mechanisms:

* Gateways periodically send metrics and peer statistics to the Core for dashboards and alerts.
* Every administrative action (e.g., user addition, configuration change) is logged for traceability.
* Logs and metrics can be exported to external monitoring systems (SIEM, Prometheus, etc.) for centralized analysis.

Continuous visibility ensures operational awareness and compliance with audit requirements.



