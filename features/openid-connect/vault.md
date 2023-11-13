# Hashicorp Vault

## Example setup

This configuration is an example, which shows how you can connect OpenID Connect with Hashicorp Vault.

Create `vault.json` with the following content:

```json
{
   "backend":{
      "file":{
         "path":"/vault/file"
      }
   },
   "listener":{
      "tcp":{
         "address":"0.0.0.0:8200",
         "tls_disable":1
      }
   },
   "default_lease_ttl":"168h",
   "max_lease_ttl":"168h",
   "ui":true,
   "log_level":"Debug"
}
```

Create `docker-compose.yaml` with the following content:

```yaml
services:
  vault:
    image: vault:latest
    container_name: vault
    environment:
      VAULT_ADDR: http://127.0.0.1:8200
    ports:
      - "8200:8200"
    volumes:
      - ./volumes/vault:/vault/file:rw
      - ./vault.json:/vault/config/vault.json:rw
    cap_add:
      - IPC_LOCK
    entrypoint: vault server -config=/vault/config/vault.json -dev
```

Run it using `docker-compose up` command.

Create root token using `docker exec -it vault vault operator init -n 1 -t 1`, write down the root token and unseal key.

## Defguard configuration

1. Go to **OpenID Apps** and click **Add new** button.
2. Use Scopes: `openid email profile`
3. Use `http://127.0.0.1:8200/ui/vault/auth/oidc/oidc/callback` as **Redirect URI**
4. Copy and save **Client ID** and **Client secret**, we will need them later.

## Vault configuration


1. Unseal vault by accessing `http://127.0.0.1:8200/ui` and using unseal key.
2. Login into Vault using method `TOKEN` and using root token.
3. Navigate to **Access -> Auth Methods** and click **Enable new method** button.
4. Enable **OIDC** method.
5. Use values below:
`OIDC discovery URL: https://defguard.company.net/`
`OIDC client ID: <YOUR_CLIENT_ID>`
`OIDC secret ID: <YOUR_CLIENT_SECRET>`

## Creating role in vault
1. Login into vault CLI using root token: `docker exec -it vault vault login <ROOT_TOKEN>`
2. To create role `reader` use command below:
```bash
docker exec -it vault vault write auth/oidc/role/reader \
    bound_audiences="<YOUR_CLIENT_ID>" \
    allowed_redirect_uris="https://127.0.0.1:8200/ui/vault/auth/oidc/oidc/callback" \
    user_claim="sub" \
    token_policies="default"
```

Now you can login into vault using Defguard. Use `OIDC` as login method and role `reader`. Please note this role will only allow you to login, to add permissions you need to create policy and assign it to role.