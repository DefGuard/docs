# Command-line (defguard-client)

`defguard-client` lets you use the Defguard desktop client from the terminal: connect to and disconnect from VPN locations, check connection status, and inspect your configuration. It can be used for headless machines, remote sessions over SSH or controlling your VPN connections with scripting.

The CLI shares the configuration with the desktop client - the same instances and locations, the same background service for managing WireGuard interfaces. Any instance you add in the desktop app is immediately usable from the CLI.

{% hint style="info" %}
This page covers running the **desktop client** from the terminal. If you are looking for the standalone headless agent that enrolls a machine as a **network device** and runs as a polling service, see [CLI Client (dg)](/broken/pages/yjUb0u49KJzUtjcXA4C6) instead - that is a different tool.
{% endhint %}

### Requirements

* The **Defguard desktop client** must be installed (the CLI ships with it - see [Installation](command-line-defguard-cli.md#installation)).
* You must have **enrolled at least one instance** using the desktop client. The CLI does not perform enrollment; see Instance configuration.

### Installation

`defguard-client` is included with the Defguard desktop client packages. Install the client for your platform as described in [Desktop Client](./) and the `defguard-client` command will be available.

### Quick start

```bash
# 1. List what you have configured (instances, locations, tunnels)
defguard-client list

# 2. Connect to a location by name
defguard-client connect office

# 3. Check that you're connected
defguard-client status

# 4. Disconnect when you're done
defguard-client disconnect office
```

If you have only a single location configured or only a single connection is active, you can omit the name entirely:

```bash
defguard-client connect
defguard-client disconnect
```

### Global options

These can be used with any command:

| Option              | Description                                                                  |
| ------------------- | ---------------------------------------------------------------------------- |
| `--json`            | Print machine-readable JSON instead of human-readable output.                |
| `-v`, `-vv`, `-vvv` | Increase log verbosity (INFO, DEBUG, TRACE). Logs are written to **stderr**. |
| `-V`, `--version`   | Print the version and exit.                                                  |
| `-h`, `--help`      | Show help. Works on subcommands too, e.g. `defguard-client connect --help`.  |

The log level can also be set with the `DG_LOG` (or `RUST_LOG`) environment variable.

### Commands

#### `list`

Lists all configured instances, locations, and tunnels.

```bash
$ defguard-client list
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
$ defguard-client status
NAME    TYPE      INTERFACE  TX        RX        LAST HANDSHAKE
office  location  dg0        1.2 MiB   8.4 MiB   12s ago
```

#### `connect` (alias: `c`)

Connects to a location or tunnel.

```bash
# By name
defguard-client connect office

# A tunnel instead of a location
defguard-client connect backup --tunnel

# Disambiguate when the same location name exists in two instances
defguard-client connect office --instance acme

# Target directly by ID (skips name resolution)
defguard-client connect --id 1
```

On success:

```bash
$ defguard-client connect office
Connected to office
```

`connect` is idempotent - connecting to something already connected succeeds and tells you so.

**Routing override** (for this connection only):

```bash
defguard-client connect office --all-traffic        # route all traffic through the VPN
defguard-client connect office --predefined-traffic  # only route the configured networks
```

If the location requires multi-factor authentication, see [Multi-factor authentication](command-line-defguard-cli.md#multi-factor-authentication) below.

#### `disconnect` (alias: `d`)

Disconnects from a location or tunnel.

```bash
defguard-client disconnect office          # by name
defguard-client disconnect backup --tunnel # a tunnel
defguard-client disconnect --all           # everything currently connected
```

With no name and a single active connection, it disconnects that one. With multiple active connections and no name, it asks you to be specific.

#### `location` (alias: `l`)

Manage per-location settings.

```bash
# List locations
defguard-client location list

# Show one location in detail
defguard-client location show office
```

```bash
$ defguard-client location show office
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
defguard-client location set office --mfa-method totp

# Always / never route all traffic through this location
defguard-client location set office --route-all-traffic
defguard-client location set office --predefined-traffic
```

#### `instance` (alias: `i`)

```bash
defguard-client instance list
defguard-client instance show acme
```

#### `tunnel` (alias: `t`)

```bash
defguard-client tunnel list
defguard-client tunnel show backup
```

### Multi-factor authentication

If a location requires MFA, `connect` will ask for verification before bringing the connection up.

#### Entering a code (TOTP or email)

When run in an interactive terminal, the CLI prompts you:

```bash
$ defguard-client connect office
Enter MFA code for office: 123456
Connected to office
```

You can also supply the code up front, which is useful in scripts:

```bash
# Pass the code directly
defguard-client connect office --code 123456

# Or have a command produce it (e.g. from a secret manager).
# The command receives DG_INSTANCE and DG_LOCATION in its environment.
# For a multi-step location it runs once for each step that needs a code
# and also receives DG_MFA_STEP, DG_MFA_STEP_COUNT and DG_MFA_METHOD
defguard-client connect office --code-command "pass otp defguard"
```

#### One-time login (OIDC)

When a verification step uses OpenID, the CLI opens your default web browser to complete sign-in with your identity provider. A location whose only method is OpenID uses it automatically. Otherwise, choose it like any other method, with `--mfa-method oidc` for a single-step location or `--mfa-step oidc` for that step of a multi-step location (see [How the method is chosen](command-line-defguard-cli.md#how-the-method-is-chosen))

```bash
defguard-client connect office --mfa-method oidc
```

#### Mobile approval

For mobile-approve MFA, the CLI displays a QR code in the terminal for you to scan with the Defguard mobile app. When running where a QR code cannot be shown (for example, a non-interactive script), save it to an image instead:

```bash
defguard-client connect office --qr-file /tmp/defguard-qr.png
```

#### How the method is chosen

A location's [MFA flow](../../features/wireguard/multi-factor-authentication-mfa-2fa/mfa-flows-and-methods.md) has one or more verification steps, and each step allows one or more methods. The CLI can run `totp`, `email`, `oidc` and `mobile`. It cannot run `fido2` (security key) or `biometric`; a step that allows only those fails with a message telling you to use the desktop or mobile client.

**Single-step locations.** You usually don't need to pick a method - the CLI infers it from the location:

* A location whose only method is OpenID always authenticates through your identity provider, so the CLI uses the OIDC browser flow.
* Other locations use the method saved for that location - TOTP, email, or mobile approval. If no method is saved, the CLI uses **TOTP**.

You can override the inferred method for a single connection with `--mfa-method`:

```bash
defguard-client connect office --mfa-method email
```

Valid values are `totp`, `email`, `oidc`, and `mobile`. Passing `oidc` for a location that does not use OpenID is rejected (exit code `7`).

To change the method a location remembers - so you don't have to repeat `--mfa-method` every time - use `location set`:

```bash
defguard-client location set office --mfa-method totp
```

**Multi-step locations.** The CLI picks one method per step: a method given on the command line, else the method saved for that step, else the step's only method the CLI can run. When a step still has more than one candidate, the CLI asks you to choose in an interactive terminal, and fails with exit code `5` otherwise.

Choose methods for one connection by repeating `--mfa-step` in step order. Steps you leave out are chosen as described above:

```bash
defguard-client connect office --mfa-step totp --mfa-step mobile
```

After an interactive choice, the CLI prints the `location set` command that saves it. Save a method for every step the same way:

```bash
defguard-client location set office --mfa-step totp --mfa-step mobile
```

`location set` also accepts `fido2` and `biometric`. The CLI cannot connect with them, but it saves them because the desktop client reads the same saved methods.

`--mfa-method` works only for single-step locations and `--mfa-step` only for multi-step ones; using the wrong one is rejected. `--code` passes a single code, so for a multi-step location use `--code-command`, which runs once per step, or an interactive terminal.

### Scripting & automation

`defguard-client` is designed to be scriptable, so you can automate your VPN connection management without giving up MFA.

#### JSON output

Add `--json` to any command for machine-readable output. Data is written to **stdout**; logs and prompts go to **stderr**, so piping is safe:

```bash
defguard-client list --json | jq -r '.locations[].name'
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
defguard-client connect office --code-command "pass otp defguard" --json
```

If MFA input is needed but cannot be obtained (no terminal, and no `--code` / `--code-command` / `--qr-file` provided), the command fails with exit code `5` rather than hanging.
