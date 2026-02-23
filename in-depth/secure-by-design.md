# Secure By Design

Defguard, unlike most systems that focus primarily on functionality prioritizes **security first and then builds functionality on top of secure foundations**. It is developed using a [Secure by Design approach](https://en.wikipedia.org/wiki/Secure_by_design), with the following principles embedded in both architecture and code:

1. Least Privilege / Zero Trust
2. Segmentation of systems and components
3. Secure technology stack
4. Capability for inspection and verification
5. Security audits and testing

Below, you will find detailed information on how each of these principles has been designed and implemented.

### Zero Trust

Zero Trust means that **no user or device is trusted by default**. Every access request to a resource must be explicitly authenticated and authorized. The most effective modern implementation of this principle includes strong identity verification mechanisms such as Multi-Factor Authentication (MFA)combined with robust access control policies.

To address this, **Defguard enforces Multi-Factor Authentication for every VPN connection**. Unlike many cloud-based VPN solutions that apply MFA only to access the configuration control plane, Defguard requires MFA during the actual VPN connection process, ensuring that authentication is enforced at the data plane level, not just the management interface.

### Segmentation of systems and components

Defguard separates all components and enables **deployment of the main component** (Core) which is responsible for business logic and has access to user data (e.g., via integration with SSO, LDAP, or Active Directory) **in a fully isolated environment**. The Core can be deployed without direct Internet exposure and is not accessible from application components that are publicly exposed (e.g., the Edge Proxy component).

In contrast, many cloud-based VPN solutions expose all core components to the public Internet, increasing their potential attack surface and making them more susceptible to compromise.

More details are available in the [Architecture chapter](architecture/) of this documentation.

### Secure technology stack

The choice of programming language and frameworks/libraries is fundamental, as it directly affects:

* Exposure to vulnerabilities
* The likelihood of implementation errors
* System behavior under failure conditions
* The ability to detect and mitigate attacks

Defguard is built in Rust, which is recommended by leading security organizations worldwide due to its strong memory safety guarantees and modern security model:

* NSA:[ https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3215760/](https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3215760/)
* CISA/NSA Joint:[ https://www.cisa.gov/sites/default/files/2023-12/The-Case-for-Memory-Safe-Roadmaps-508c.pdf](https://www.cisa.gov/sites/default/files/2023-12/The-Case-for-Memory-Safe-Roadmaps-508c.pdf)
* DARPA:[ https://www.darpa.mil/research/programs/translating-all-c-to-rust](https://www.darpa.mil/research/programs/translating-all-c-to-rust)
* French ANSSI Rust Guide:[ https://github.com/ANSSI-FR/rust-guide](https://github.com/ANSSI-FR/rust-guide)

### Inspection, verification & security audits

To fulfill this principle, the system must meet the following standards:

* Code and architecture transparency – components, algorithms, and data flows should be analyzable.
* Logs and audit trails – every significant operation (e.g., who did what and when) must be recorded.
* Documentation and requirements traceability – linking requirements to implementation, testing, and analysis.
* Openness to independent inspection – allowing external auditors or regulators to assess security and compliance.

Defguard is open source, and the Enterprise components are fully open as well. To our knowledge, it is the only VPN solution that openly publishes:

* Periodic security audits: [https://defguard.net/pentesting/](https://defguard.net/pentesting/)
* Daily vulnerability reports (CVE) for all software components (SBOM): [https://defguard.net/sbom/](https://defguard.net/sbom/)
* Full roadmap, development process, and [Architecture Decision Records](architecture-decision-records/)

<br>
