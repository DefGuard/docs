# Command-line (defguard-cli)

`defguard-cli` lets you use the Defguard desktop client from the terminal: connect to and disconnect from VPN locations, check connection status, and inspect your configuration. It can be used for headless machines, remote sessions over SSH or controlling your VPN connections with scripting.

The CLI shares the configuration with the desktop client - the same instances and locations, the same background service for managing WireGuard interfaces. Any instance you add in the desktop app is immediately usable from the CLI.

{% hint style="info" %}
This page covers running the **desktop client** from the terminal. If you are looking for the standalone headless agent that enrolls a machine as a **network device** and runs as a polling service, see [CLI Client (dg)](../cli-client.md) instead - that is a different tool.
{% endhint %}

### Requirements

* The **Defguard desktop client** must be installed (the CLI ships with it - see [Installation](command-line-defguard-cli.md#installation)).
* You must have **enrolled at least one instance** using the desktop client. The CLI does not perform enrollment; see Instance configuration.

### Installation

`defguard-cli` is included with the Defguard desktop client packages. Install the client for your platform as described in [Desktop Client](./) and the `defguard-cli` command will be available.

### Quick start

```bash
# 1. List what you have configured (instances, locations, tunnels)
defguard-cli list

# 2. Connect to a location by name
defguard-cli connect office

# 3. Check that you're connected
defguard-cli status

# 4. Disconnect when you're done
defguard-cli disconnect office
```

If you have only a single location configured or only a single connection is active, you can omit the name entirely:

```bash
defguard-cli connect
defguard-cli disconnect
```

### Global options

These can be used with any command:

| Option              | Description                                                                  |
| ------------------- | ---------------------------------------------------------------------------- |
| `--json`            | Print machine-readable JSON instead of human-readable output.                |
| `-v`, `-vv`, `-vvv` | Increase log verbosity (INFO, DEBUG, TRACE). Logs are written to **stderr**. |
| `-V`, `--version`   | Print the version and exit.                                                  |
| `-h`, `--help`      | Show help. Works on subcommands too, e.g. `defguard-cli connect --help`.     |

The log level can also be set with the `DG_LOG` (or `RUST_LOG`) environment variable.

### Commands

#### `list`

Lists all configured instances, locations, and tunnels.

```bash
$ defguard-cli list
Instances
  NAME   URL
  acme   https://vpn.acme.com

Locations
  ID  NAME    ADDRESS     ENDPOINT             MFA    ROUTING
  1   office  10.0.0.2    vpn.acme.com:51820   totp   predefined
  2   prod    10.1.0.2    vpn.acme.com:51820   -      all traffic

Tunnels
  ID  NAME    ADDRESS     ENDPOINT             ROUTING
  1   backup  10.9.0.2    backup.example:51820 predefined
```

#### `status` (alias: `s`)

Shows currently-active connections with live statistics read from the running tunnels.

```bash
$ defguard-cli status
NAME    TYPE      INTERFACE  TX        RX        LAST HANDSHAKE
office  location  dg0        1.2 MiB   8.4 MiB   12s ago
```

#### `connect` (alias: `c`)

Connects to a location or tunnel.

```bash
# By name
defguard-cli connect office

# A tunnel instead of a location
defguard-cli connect backup --tunnel

# Disambiguate when the same location name exists in two instances
defguard-cli connect office --instance acme

# Target directly by ID (skips name resolution)
defguard-cli connect --id 1
```

On success:

```bash
$ defguard-cli connect office
Connected to office
```

`connect` is idempotent - connecting to something already connected succeeds and tells you so.

**Routing override** (for this connection only):

```bash
defguard-cli connect office --all-traffic        # route all traffic through the VPN
defguard-cli connect office --predefined-traffic  # only route the configured networks
```

If the location requires multi-factor authentication, see [Multi-factor authentication](command-line-defguard-cli.md#multi-factor-authentication) below.

#### `disconnect` (alias: `d`)

Disconnects from a location or tunnel.

```bash
defguard-cli disconnect office          # by name
defguard-cli disconnect backup --tunnel # a tunnel
defguard-cli disconnect --all           # everything currently connected
```

With no name and a single active connection, it disconnects that one. With multiple active connections and no name, it asks you to be specific.

#### `location` (alias: `l`)

Manage per-location settings.

```bash
# List locations
defguard-cli location list

# Show one location in detail
defguard-cli location show office
```

```bash
$ defguard-cli location show office
Name:              office
Address:           10.0.0.2
Endpoint:          vpn.acme.com:51820
Pubkey:            xTIB…=
Allowed IPs:       10.0.0.0/24
DNS:               10.0.0.1
MFA method:        totp
Route all traffic: false
Keepalive:         25s
```

Persist a preference for a location with `location set`:

```bash
# Set which MFA method this location uses by default
defguard-cli location set office --mfa-method totp

# Always / never route all traffic through this location
defguard-cli location set office --route-all-traffic
defguard-cli location set office --predefined-traffic
```

#### `instance` (alias: `i`)

```bash
defguard-cli instance list
defguard-cli instance show acme
```

#### `tunnel` (alias: `t`)

```bash
defguard-cli tunnel list
defguard-cli tunnel show backup
```

### Multi-factor authentication

If a location requires MFA, `connect` will ask for verification before bringing the connection up.

#### Entering a code (TOTP or email)

When run in an interactive terminal, the CLI prompts you:

```bash
$ defguard-cli connect office
Enter MFA code for office: 123456
Connected to office
```

You can also supply the code up front, which is useful in scripts:

```bash
# Pass the code directly
defguard-cli connect office --code 123456

# Or have a command produce it (e.g. from a secret manager).
# The command receives DG_INSTANCE and DG_LOCATION in its environment.
defguard-cli connect office --code-command "pass otp defguard"
```

#### One-time login (OIDC)

For locations that use an external identity provider, the CLI opens your default web browser to complete sign-in:

```bash
defguard-cli connect office --mfa-method oidc
```

#### Mobile approval

For mobile-approve MFA, the CLI displays a QR code in the terminal for you to scan with the Defguard mobile app. When running where a QR code cannot be shown (for example, a non-interactive script), save it to an image instead:

```bash
defguard-cli connect office --qr-file /tmp/defguard-qr.png
```

#### How the method is chosen

You usually don't need to pick a method - the CLI infers it from how the location is configured on the server:

* Locations that use **external (OIDC) MFA** always authenticate through your identity provider, so the CLI uses the OIDC browser flow.
* Locations that use Defguard's **internal MFA** use whichever method that location is set to - TOTP, email, or mobile approval. If no specific method is set, the CLI uses **TOTP**.

You can override the inferred method for a single connection with `--mfa-method`:

```bash
defguard-cli connect office --mfa-method email
```

Valid values are `totp`, `email`, `oidc`, and `mobile`. The override has one restriction: `oidc` is only valid for locations that use external (OIDC) MFA - passing it for an internal-MFA location is rejected (exit code `7`).

To change the default method a location remembers - so you don't have to repeat `--mfa-method` every time - use `location set` (this applies to internal-MFA locations; external locations always use OIDC):

```bash
defguard-cli location set office --mfa-method totp
```

### Scripting & automation

`defguard-cli` is designed to be scriptable, so you can automate your VPN connection management without giving up MFA.

#### JSON output

Add `--json` to any command for machine-readable output. Data is written to **stdout**; logs and prompts go to **stderr**, so piping is safe:

```bash
defguard-cli list --json | jq -r '.locations[].name'
```

#### Exit codes

Every command returns a meaningful exit code:

| Code | Meaning                                                 |
| ---- | ------------------------------------------------------- |
| `0`  | Success                                                 |
| `1`  | Database or other unclassified error                    |
| `2`  | Invalid usage (bad flag or argument)                    |
| `3`  | Location, tunnel, or instance not found                 |
| `4`  | Background service unavailable                          |
| `5`  | MFA failed, or MFA input was required but not available |
| `6`  | No instances enrolled                                   |
| `7`  | Invalid input (e.g. unsupported MFA method override)    |
| `8`  | Cancelled by the user                                   |

#### Unattended connection

To connect without any interactive prompt - for example from a startup script - provide the MFA code non-interactively:

```bash
#!/usr/bin/env bash
set -e
defguard-cli connect office --code-command "pass otp defguard" --json
```

If MFA input is needed but cannot be obtained (no terminal, and no `--code` / `--code-command` / `--qr-file` provided), the command fails with exit code `5` rather than hanging.
