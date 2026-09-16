# Zero-Trust VPN with 2FA/MFA

## Defguard is based on WireGuard®

WireGuard® compared to any other VPN solution on the market provides:

* Faster VPN Speeds: WireGuard® is \~10x faster then OpenVPN - since it’s on kernel and protocol level and not application level (like OpenVPN) and significantly faster then IPSec.
* Seamless Roaming: WireGuard® is designed to handle network changes (like switching from Wi-Fi to cellular) more gracefully than any other VPN, maintaining the connection without interruption - whereas OpenVPN and IPSec looses connections on network change.
* Lower VPN Latency: WireGuard® has far lower latency due to its lightweight design.
* Instant Connectivity: WireGuard’s handshakes are very fast, allowing near-instantaneous connections, unlike OpenVPN or IPSec, which can take a few seconds to establish a connection.

## Zero-Trust with 2FA/MFA

Defguard introduces unique Multi-Factor Authentication (MFA) for the WireGuard® VPN protocol, ensuring every connection requires authorization with MFA (human factor + session keys) enhancing security with an added layer of user verification to support compliance with GDPR, HIPAA, PCI DSS, NIST, FISMA, and CMMC standards.
