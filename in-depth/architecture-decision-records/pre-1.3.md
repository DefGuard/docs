# Pre-1.3

## 2025-01-03 – Multiple IP addresses

WireGuard network interface can be assigned multiple IP addresses (both IPv4 and IPv6). The first address (leftmost on the interface configuration) is the primary address, and this one will be used for IP address assignment for devices. The other IP addresses are auxiliary and are not managed by Defguard.

## 2024-11-14 – Use Authorization Code flow for external OpenID

Abandon Implicit flow, which is not recommended.

## 2024-11-11 – Require User-Agent HTTP header for login

User-Agent header is set by all HTTP clients, including [Curl](https://curl.se), so there is no point to make it optional. That also simplifies the code.

## 2024-09-07 - Typestate pattern to make working with optional ids easier

* There is a recurring issue with optional ids for database objects. Until such object is saved to database, it’s id is none. Because of this it is necessary to check for id presence whenever such objects are used, even if we know that those objects come from database and must have ids. This leads to unnecessary `.expect() / .unwrap()`s.
* Typical object with optional Id looks like this:

```
#[derive(Debug)]
pub struct WireguardKeys {
    pub id: Option<i64>,
    pub instance_id: i64,
    pub pubkey: String,
    pub prvkey: String,
}
```

* To fix this we introduce a generic type \<I> that defines type of id field
* The generic type can take one of two variants
* We split impl blocks into \<Id> and \<NoId> appropriately - the idea is that objects coming from database are \<Id>, methods like `new()` return \<NoId>

```
// Id variants
pub type Id = i64;
pub struct NoId;

// Generic id type used in the structure
#[derive(Debug)]
pub struct WireguardKeys<I = NoId> {
    pub id: I,
    pub instance_id: i64,
    pub pubkey: String,
    pub prvkey: String,
}

// Impl blocks split between the variants
impl WireguardKeys<Id> {
    pub async fn find_by_instance_id<'e, E>(
        executor: E,
        instance_id: i64,
    ) -> Result<Option<Self>, SqlxError>
        where
            E: sqlx::Executor<'e, Database = sqlx::Sqlite>,
    {
        query_as!(
            Self,
            "SELECT id \"id: _\", instance_id, pubkey, prvkey \
            FROM wireguard_keys WHERE instance_id = $1;",
            instance_id
        )
        .fetch_optional(executor)
        .await
    }
}

impl WireguardKeys<NoId> {
    #[must_use]
    pub fn new(instance_id: i64, pubkey: String, prvkey: String) -> Self {
        WireguardKeys {
            id: NoId,
            instance_id,
            pubkey,
            prvkey,
        }
    }
}
```

## 2024-09-06 – External OpenID login

* Currently our OpenID login implementation matches the user by email. This is not a standard practice, as most services use the “sub” field (a guaranteed unique identifier) to identify the user (e.g. matrix/element). Our approach may be problematic when the email changes on the provider’s side (it may be unlikely in the case of google or Microsoft but may happen in the case of keycloak).
* To prevent such scenario and to standardize our approach, we could add a “sub” field to the user and perform OpenID login on its basis. Additionally, if we’d like to link existing Defguard accounts with the external provider, we could try to also match by email on first user login and then use the sub field only on subsequent login attempts. This doesn’t seem to require a massive rework of already existing code.

## 2024-09-02 – Client configuration updates

* Since client <→ proxy communication is REST, easiest way to implement client config updates (“make it work, make it right, make it fast”, “good enough”) is with HTTP polling requests
* As a consequence, updates are not immediate, worst case scenario update arrives at client after full polling interval
* Objects that DON’T need to be updated in the client database:
  * WireGuard keys - since client does not implement key management features, updating public key in client db guarantees that the client won’t be able to connect to the gateway (public-private keys don’t match)
  * only way to update keys at this point is using standard enrollment procedure
  * Instances - effectively only the `name` field may change, we can deal with it later maybe
* Objects that need to be updated in client DB:
  * Instance locations - those contain all the necessary configuration that make connecting to the gateway possible and define parameters like `allowed_ips`, `dns` etc.
* We can’t simply update the locations whenever they change, client may be connected to one of the locations that get removed in the update and we end up in invalid state.
* Solution:
  * After detecting changes in the configuration, polling mechanism checks if there are any active connections for given instance
  * If there are no active connections, update the database
  * If there are active connections, display the message “Configuration updated for instance \<name>, disconnect all locations to apply changes”
  * On each disconnect trigger the polling mechanism

