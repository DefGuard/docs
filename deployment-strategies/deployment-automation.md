# Deployment automation

This guide will focus on ways of automating some aspects of the deployment of the Defguard components.

### Gateway secret

The Gateway secret is one of the [Core's configuration values](configuration.md#secrets-configuration) and is used to generate [Gateway authentication tokens](gateway.md). This value must be often generated automatically and then passed to components to achieve a fully automated process.

The easiest way of generating the `DEFGUARD_GATEWAY_SECRET` would be using the following bash command:

```bash
openssl rand -base64 64 | tr -d "=+/" | tr -d '\n' | cut -c1-"64"
```

### First location creation

You can programmatically add a first network (location) by invoking the following command (using the Defguard binary):

```bash
defguard --secret-key "<DEFGUARD_GATEWAY_SECRET>" init-vpn-location \
--name <NETWORK_NAME> \
--address <VPN_NETWORK_ADDRESS> \
--endpoint <GATEWAY_ENDPOINT> \
--port <GATEWAY_VPN_PORT> \
--id 1 \
--allowed-ips <ALLOWED_IP> \
--allowed-ips <ALLOWED_IP> 2>&1 \
| grep -Eo '^[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+$'
```

For example:

```bash
defguard --secret-key "<DEFGUARD_GATEWAY_SECRET>" init-vpn-location \
--name network-name \
--address 10.10.10.1/24 \
--endpoint 127.0.0.1 \
--port 50051 \
--id 1 \
--allowed-ips 10.10.10.1/24 \
--allowed-ips 10.10.11.1/24 2>&1 \
| grep -Eo '^[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+\.[A-Za-z0-9_-]+$'
```

The grep at the end is used to extract the Gateway token (JWT) that the command returns. The token should be [passed to the Gateway](configuration.md#gateway-configuration) in order to authenticate to Core.

The Defguard binary location is dependent on the deployment method, an absolute path may be required here, e.g. `/usr/bin/defguard`.

If the command doesn't produce an output, try running it without the grep, to check for any errors.

### Gateway token

If for some reason you can't rely on the token that's output by the `init-vpn-location` command described in the [previous section](deployment-automation.md#first-location-creation), you can generate the token independently using scripts.

To generate a Gateway authentication token, the Core's `DEFGUARD_GATEWAY_SECRET` value must be available to the script. Thus, your deployment process must generate the `DEFGUARD_GATEWAY_SECRET` before generating the token, and share the value of the secret, to the script that generates the token.

The following example script can be used to generate Gateway's authentication token:

```bash
#!/bin/bash

base64url_encode() {
    echo -n "$1" | openssl base64 -e -A | tr '+/' '-_' | tr -d '='
}

# The ID of the DefGuard network for which the gateway token is generated,
# if your deployment creates only one (first) network, this should be "1".
NETWORK_ID="1"
ISSUER="DefGuard"

HEADER='{"alg":"HS256","typ":"JWT"}'
NOW=$(date +%s)
EXPIRATION=$(($NOW + 315360000))
PAYLOAD=$(cat <<EOF
{
    "iss": "$ISSUER",
    "sub": "DEFGUARD-NETWORK-$NETWORK_ID",
    "client_id": "$NETWORK_ID",
    "exp": $EXPIRATION,
    "nbf": $NOW
}
EOF
)
HEADER_B64=$(base64url_encode "$HEADER")
PAYLOAD_B64=$(base64url_encode "$PAYLOAD")
SIGNING_INPUT="$HEADER_B64.$PAYLOAD_B64"
SIGNATURE=$(echo -n "$SIGNING_INPUT" | openssl dgst -sha256 -hmac "$DEFGUARD_GATEWAY_SECRET" -binary | openssl base64 -e -A | tr '+/' '-_' | tr -d '=')

GATEWAY_TOKEN="$SIGNING_INPUT.$SIGNATURE"
echo "$GATEWAY_TOKEN"

```

The above script requires the environment variable `DEFGUARD_GATEWAY_SECRET` to be set.

Example execution:

```bash
DEFGUARD_GATEWAY_SECRET=<YOUR_GATEWAY_SECRET> ./token.sh
```

The output token can be then used for [configuring the Gateway](configuration.md#gateway-configuration).

