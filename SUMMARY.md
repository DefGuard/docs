# Table of contents

* [Welcome](README.md)
* [Getting help](support.md)

## About

* [About defguard](about/about-defguard.md)
* [Features overview](about/features-overview.md)

## Getting started

* [One-line install script](getting-started/one-line-install.md)

## Features <a href="#admin-and-features" id="admin-and-features"></a>

* [Overview](admin-and-features/overview.md)
* [Zero-Trust VPN with 2FA/MFA](admin-and-features/wireguard/README.md)
  * [Create/manage VPN Location](admin-and-features/wireguard/create-your-vpn-network.md)
  * [Network overview](admin-and-features/wireguard/network-overview.md)
  * [Executing custom gateway commands](admin-and-features/wireguard/executing-custom-gateway-commands.md)
  * [Multi-Factor Authentication (MFA/2FA)](admin-and-features/wireguard/multi-factor-authentication-mfa-2fa/README.md)
    * [MFA Architecture](admin-and-features/wireguard/multi-factor-authentication-mfa-2fa/architecture.md)
  * [Remote desktop client configuration](admin-and-features/wireguard/remote-desktop-activation.md)
  * [DNS and domains](admin-and-features/wireguard/dns-and-domains.md)
  * [VPN & Client behavior customization](admin-and-features/wireguard/behavior-customization.md)
* [Remote user enrollment](admin-and-features/remote-user-enrollment/README.md)
  * [User onboarding after enrollment](admin-and-features/remote-user-enrollment/user-onboarding-after-enrollment.md)
  * [Automatic (real time) desktop client configuration & sync](admin-and-features/remote-user-enrollment/automatic-real-time-desktop-client-configuration.md)
* [SSO (OpenID Connect)](admin-and-features/openid-connect/README.md)
  * [Portainer](admin-and-features/openid-connect/portainer.md)
  * [Grafana setup](admin-and-features/openid-connect/grafana-setup.md)
  * [Proxmox](admin-and-features/openid-connect/proxmox.md)
  * [Matrix / Synapse](admin-and-features/openid-connect/proxmox-1.md)
  * [Django](admin-and-features/openid-connect/django.md)
  * [MinIO](admin-and-features/openid-connect/minio.md)
  * [Vault](admin-and-features/openid-connect/vault.md)
* [LDAP and Active Directory integration](admin-and-features/ldap-and-active-directory-integration/README.md)
  * [Configuration](admin-and-features/ldap-and-active-directory-integration/configuration.md)
  * [Settings table](admin-and-features/ldap-and-active-directory-integration/settings-table.md)
  * [Two-way LDAP and Active Directory synchronization](admin-and-features/ldap-and-active-directory-integration/two-way-ldap-and-active-directory-synchronization.md)
* [Access Control List](admin-and-features/access-control-list/README.md)
  * [ACL Aliases](admin-and-features/access-control-list/acl-aliases.md)
  * [Implementation Details](admin-and-features/access-control-list/firewall-internals.md)
* [External OpenID providers](admin-and-features/external-openid-providers/README.md)
  * [Google](admin-and-features/external-openid-providers/google.md)
  * [Microsoft](admin-and-features/external-openid-providers/microsoft.md)
  * [Zitadel](admin-and-features/external-openid-providers/zitadel.md)
  * [Keycloak](admin-and-features/external-openid-providers/keycloak.md)
  * [JumpCloud](admin-and-features/external-openid-providers/jumpcloud.md)
  * [Okta](admin-and-features/external-openid-providers/okta.md)
  * [Custom](admin-and-features/external-openid-providers/custom.md)
  * [External OIDC secure enrollment](admin-and-features/external-openid-providers/external-oidc-secure-enrollment.md)
* [SSH Authentication](admin-and-features/ssh-authentication.md)
* [Forward auth](admin-and-features/forward-auth.md)
* [YubiKey Provisioning](admin-and-features/yubikey-provisioning.md)
* [Network devices](admin-and-features/network-devices.md)

***

* [Activity & Audit logs](activity-log/README.md)
  * [Audit Log Streaming to SIEM systems](activity-log/activity-log-streaming/README.md)
    * [Supported SIEM systems integrations](activity-log/activity-log-streaming/activity-log-integrations/README.md)
      * [Vector integration guide](activity-log/activity-log-streaming/activity-log-integrations/vector-integration-guide.md)
      * [Logstash integration guide](activity-log/activity-log-streaming/activity-log-integrations/logstash-integration-guide.md)
* [Notifications](notifications/README.md)
  * [Email notifications](notifications/setting-up-smtp-for-email-notifications.md)
  * [Gateway notifications](notifications/gateway-notifications.md)
  * [New version notifications](notifications/new-version-notifications.md)
* [Integrations](integrations/README.md)
  * [Webhooks](integrations/webhooks.md)
  * [REST API](integrations/api-tokens.md)
* [User SNAT bindings](user-snat-bindings.md)

## Deployment strategies

* [Overview](deployment-strategies/setting-up-your-instance.md)
* [Standalone package based installation](deployment-strategies/standalone-package-based-installation.md)
* [Docker images and tags](deployment-strategies/docker-images-and-tags.md)
* [Docker Compose](deployment-strategies/docker-compose.md)
* [Kubernetes](deployment-strategies/kubernetes.md)
* [Terraform](deployment-strategies/terraform.md)
* [High Availability and Failover](deployment-strategies/high-availability-and-failover.md)
* [Upgrading](deployment-strategies/upgrading.md)
* [Pre-production and development releases](deployment-strategies/pre-production-and-development-releases.md)
* [Gateway](deployment-strategies/gateway/README.md)
  * [Running gateway on MikroTik routers](deployment-strategies/gateway/running-gateway-on-mikrotik-routers.md)
* [Securing gRPC communication](deployment-strategies/grpc-ssl-communication.md)

***

* [OpenID RSA key](openid-rsa-key.md)
* [Health check](health-check.md)
* [Configuration](configuration.md)

## Enterprise

* [License](enterprise/license.md)
* [Enterprise features](enterprise/enterprise-features.md)

## Using Defguard (for end users) <a href="#help" id="help"></a>

* [Overview](help/overwiew.md)
* [Desktop Client](help/desktop-client.md)
* [Mobile Client](help/mobile-client.md)
* [CLI Client](help/cli-client.md)
* [Configuring VPN](help/configuring-vpn/README.md)
  * [Defguard Desktop Client](help/configuring-vpn/add-new-instance/README.md)
    * [Update instance](help/configuring-vpn/add-new-instance/update-instance.md)
  * [Other WireGuard® Clients](help/configuring-vpn/adding-wireguard-devices/README.md)
    * [Configuring a device for new VPN Location manually](help/configuring-vpn/adding-wireguard-devices/configuring-a-device-for-a-new-vpn-location.md)
* [Password change / Reset](help/changing-your-password.md)
* [Enrollment & Onboarding](help/enrollment/README.md)
  * [With internal Defguard SSO](help/enrollment/with-internal-defguard-sso.md)
  * [With external SSO (Google/Microsoft/Custom)](help/enrollment/with-external-sso-google-microsoft-custom.md)
* [Setting up 2FA/MFA](help/setting-up-2fa-mfa.md)

## Tutorials

* [Step by step setting up a VPN server](tutorials/step-by-step-setting-up-a-vpn-server/README.md)
  * [Adding additional VPN locations](tutorials/step-by-step-setting-up-a-vpn-server/adding-additional-vpn-locations.md)

## In depth

* [Architecture](in-depth/architecture/README.md)
  * [How do VPN statistics work](in-depth/architecture/how-do-vpn-statistics-work.md)
  * [Security concepts](in-depth/architecture/security-concepts.md)
* [Roadmap](features/roadmap.md)
* [Release cycle](in-depth/release-cycle.md)

## For Developers

* [Contributing](for-developers/contributing.md)
* [Environment setup](for-developers/dev-env-setup/README.md)
  * [Translations (core/web)](for-developers/dev-env-setup/translations-web/README.md)
    * [Switching language](for-developers/dev-env-setup/translations-web/switching-language.md)
    * [Adding translations](for-developers/dev-env-setup/translations-web/adding-translations.md)
  * [Translations (client)](for-developers/dev-env-setup/translations-client/README.md)
    * [Adding translations](for-developers/dev-env-setup/translations-client/adding-translations.md)

## Resources

* [Troubleshooting Guide](resources/troubleshooting/README.md)
  * [Sending support information](resources/troubleshooting/sending-support-info.md)
  * [Client Windows installer exit codes](resources/troubleshooting/windows-installer-exit-codes.md)
  * [Client "All traffic" connection issues](resources/troubleshooting/client-all-traffic-connection-issues.md)
  * [WebAuthn security keys](resources/troubleshooting/webauthn-security-keys.md)
