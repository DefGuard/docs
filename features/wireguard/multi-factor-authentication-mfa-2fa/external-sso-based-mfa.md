# External SSO based MFA

{% hint style="warning" %}
Since [version 1.5.0 ](https://app.gitbook.com/s/kHPDOBrb5X1TB8O3GsjW/features/wireguard/multi-factor-authentication-mfa-2fa)we support MFA based on external OIDC/SSO.
{% endhint %}

You can use [Internal OIDC/SSO](../../openid-connect/) - called [Internal MFA ](external-sso-based-mfa.md#internal-mfa)- to force Desktop & Mobile clients to authenticate with **TOTP & Email codes** and after that with **session keys based on WireGuard Pre-Shared Keys** (PSK). For more details about this, please refer to the [architecture section](../../../in-depth/architecture/architecture.md).
