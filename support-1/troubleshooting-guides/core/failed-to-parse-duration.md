# Failed to parse duration

### Error

{% code overflow="wrap" %}
```bash
message=Started web services thread 'main' (1) panicked at crates/defguard_core/src/db/models/wireguard_peer_stats.rs:83:54: Failed to parse duration: OutOfRangeError(()) stack backtrace: note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace. 
```
{% endcode %}

### Cause

This error occurs due to a time desynchronization between the machines or containers running Defguard components.

### Solution

Ensure that the system time is synchronized across all servers running Defguard. It is highly recommended to enable and correctly configure an NTP (Network Time Protocol) service on all your host machines to prevent clock drift.

