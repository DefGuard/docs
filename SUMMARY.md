# Table of contents

* [Welcome](README.md)
* [Getting help](support.md)

## About

* [About Defguard](about/about-defguard.md)
* [Features overview](about/features-overview.md)

## Getting started

* [One-line install script](getting-started/one-line-install.md)

## Features

* [Overview](features/overview.md)
* [Zero-Trust VPN with 2FA/MFA](features/wireguard/README.md)
  * [Create/Manage VPN Location](features/wireguard/create-your-vpn-network.md)
  * [Network overview](features/wireguard/network-overview.md)
  * [Multi-Factor Authentication (MFA/2FA)](features/wireguard/multi-factor-authentication-mfa-2fa/README.md)
    * [Internal SSO based MFA](features/wireguard/multi-factor-authentication-mfa-2fa/internal-sso-based-mfa.md)
    * [External SSO based MFA](features/wireguard/multi-factor-authentication-mfa-2fa/external-sso-based-mfa.md)
  * [Remote desktop client configuration](features/wireguard/remote-desktop-activation.md)
  * [VPN & Client behaviour customization](features/wireguard/behavior-customization.md)
  * [DNS and domains](features/wireguard/dns-and-domains.md)
  * [Executing custom gateway commands](features/wireguard/executing-custom-gateway-commands.md)
* [Remote user enrollment](features/remote-user-enrollment/README.md)
  * [User onboarding after enrollment](features/remote-user-enrollment/user-onboarding-after-enrollment.md)
  * [Automatic (real time) desktop client configuration & sync](features/remote-user-enrollment/automatic-real-time-desktop-client-configuration.md)
* [Internal SSO (OpenID Connect Provider)](features/openid-connect/README.md)
  * [Portainer](features/openid-connect/portainer.md)
  * [Grafana setup](features/openid-connect/grafana-setup.md)
  * [Proxmox](features/openid-connect/proxmox.md)
  * [Matrix / Synapse](features/openid-connect/proxmox-1.md)
  * [Django](features/openid-connect/django.md)
  * [MinIO](features/openid-connect/minio.md)
  * [Vault](features/openid-connect/vault.md)
* [External SSO/OpenID providers](features/external-openid-providers/README.md)
  * [Google](features/external-openid-providers/google.md)
  * [Microsoft](features/external-openid-providers/microsoft.md)
  * [Okta](features/external-openid-providers/okta.md)
  * [JumpCloud](features/external-openid-providers/jumpcloud.md)
  * [Keycloak](features/external-openid-providers/keycloak.md)
  * [Zitadel](features/external-openid-providers/zitadel.md)
  * [Custom](features/external-openid-providers/custom.md)
  * [External OIDC secure enrollment](features/external-openid-providers/external-oidc-secure-enrollment.md)
* [LDAP and Active Directory integration](features/ldap-and-active-directory-integration/README.md)
  * [Configuration](features/ldap-and-active-directory-integration/configuration.md)
  * [Settings table](features/ldap-and-active-directory-integration/settings-table.md)
  * [Two-way LDAP and Active Directory synchronization](features/ldap-and-active-directory-integration/two-way-ldap-and-active-directory-synchronization.md)
* [Access Control List](features/access-control-list/README.md)
  * [ACL Aliases](features/access-control-list/acl-aliases.md)
  * [Implementation Details](features/access-control-list/firewall-internals.md)
* [Network devices](features/network-devices.md)
* [Activity & Audit logs](features/activity-log/README.md)
  * [Audit Log Streaming to SIEM systems](features/activity-log/activity-log-streaming/README.md)
    * [Supported SIEM systems integrations](features/activity-log/activity-log-streaming/activity-log-integrations/README.md)
      * [Vector integration guide](features/activity-log/activity-log-streaming/activity-log-integrations/vector-integration-guide.md)
      * [Logstash integration guide](features/activity-log/activity-log-streaming/activity-log-integrations/logstash-integration-guide.md)
* [Notifications](features/notifications/README.md)
  * [Email notifications](features/notifications/setting-up-smtp-for-email-notifications.md)
  * [Gateway notifications](features/notifications/gateway-notifications.md)
  * [New version notifications](features/notifications/new-version-notifications.md)
* [Integrations](features/integrations/README.md)
  * [Webhooks](features/integrations/webhooks.md)
  * [REST API](features/integrations/api-tokens.md)
* [OPSense Configuartion](features/gateway.md)
* [SSH Authentication](features/ssh-authentication.md)
* [Forward auth](features/forward-auth.md)
* [YubiKey Provisioning](features/yubikey-provisioning.md)
* [User SNAT bindings](features/user-snat-bindings.md)

## Deployment strategies

* [Overview](deployment-strategies/setting-up-your-instance.md)
* [Hardware, OS, network and firewall recommendations](deployment-strategies/hardware-os-network-and-firewall-recommendations.md)
* [Standalone package based installation](deployment-strategies/standalone-package-based-installation.md)
* [Docker Compose](deployment-strategies/docker-compose.md)
* [Kubernetes](deployment-strategies/kubernetes.md)
* [Terraform](deployment-strategies/terraform.md)
* [AMIs and AWS CloudFormation](deployment-strategies/amis-and-aws-cloudformation.md)
* [Adding a location and getting a Gateway token](deployment-strategies/gateway.md)
* [Configuration](deployment-strategies/configuration.md)
* [Running Gateway on OPNsense firewall](deployment-strategies/running-gateway-on-opnsense-firewall.md)
* [Running Gateway on MikroTik routers](deployment-strategies/running-gateway-on-mikrotik-routers.md)
* [High Availability and Failover](deployment-strategies/high-availability-and-failover.md)
* [Updating and version compatibility](deployment-strategies/updating-and-version-compatibility.md)
* [Migration guides](deployment-strategies/upgrading.md)
* [Using a userspace wireguard-go implementation](deployment-strategies/using-a-userspace-wireguard-go-implementation.md)
* [Pre-production and development releases](deployment-strategies/pre-production-and-development-releases.md)
* [Securing gRPC communication](deployment-strategies/grpc-ssl-communication.md)
* [Using RSA instead of HMAC for OpenID key](deployment-strategies/openid-rsa-key.md)
* [Health check](deployment-strategies/health-check.md)

## Enterprise

* [License](enterprise/license.md)
* [Enterprise features](enterprise/enterprise-features.md)

## Using Defguard (for end users)

* [Overview](using-defguard-for-end-users/overwiew.md)
* [Mobile Client](using-defguard-for-end-users/mobile-client/README.md)
  * [Adding new Instance](using-defguard-for-end-users/mobile-client/instance-adding.md)
  * [Connecting to Instance](using-defguard-for-end-users/mobile-client/instance-connect.md)
  * [Managing your Instance](using-defguard-for-end-users/mobile-client/instance-manage.md)
  * [Using Biometry as MFA method](using-defguard-for-end-users/mobile-client/using-biometry-as-mfa-method.md)
* [Desktop Client](using-defguard-for-end-users/desktop-client/README.md)
  * [Instance configuration](using-defguard-for-end-users/desktop-client/instance-configuration.md)
  * [Using Multi-Factor Authentication (MFA)](using-defguard-for-end-users/desktop-client/using-multi-factor-authentication-mfa.md)
* [CLI Client](using-defguard-for-end-users/cli-client.md)
* [Other WireGuard® Clients](using-defguard-for-end-users/adding-wireguard-devices/README.md)
  * [Configuring a device for new VPN Location manually](using-defguard-for-end-users/adding-wireguard-devices/configuring-a-device-for-a-new-vpn-location.md)
* [Password change / Reset](using-defguard-for-end-users/changing-your-password.md)
* [Enrollment & Onboarding](using-defguard-for-end-users/enrollment/README.md)
  * [With internal Defguard SSO](using-defguard-for-end-users/enrollment/with-internal-defguard-sso.md)
  * [With external SSO (Google/Microsoft/Custom)](using-defguard-for-end-users/enrollment/with-external-sso-google-microsoft-custom.md)
* [Setting up 2FA/MFA](using-defguard-for-end-users/setting-up-2fa-mfa.md)

## Support

* [How to submit an issue](support-1/how-to-submit-an-issue.md)
* [Troubleshooting Guide](support-1/troubleshooting/README.md)
  * [Sending support information](support-1/troubleshooting/sending-support-info.md)
  * [Client Windows installer exit codes](support-1/troubleshooting/windows-installer-exit-codes.md)
  * [Client "All traffic" connection issues](support-1/troubleshooting/client-all-traffic-connection-issues.md)
  * [WebAuthn security keys](support-1/troubleshooting/webauthn-security-keys.md)

## Tutorials

* [Step by step setting up a VPN server](tutorials/step-by-step-setting-up-a-vpn-server/README.md)
  * [Adding additional VPN locations](tutorials/step-by-step-setting-up-a-vpn-server/adding-additional-vpn-locations.md)
* [Server migration and licence transfer](tutorials/server-migration-and-licence-transfer.md)

## In depth

* [Architecture Decision Records](in-depth/architecture-decision-records/README.md)
  * [1.5](in-depth/architecture-decision-records/1.5.md)
  * [1.4](in-depth/architecture-decision-records/1.4.md)
  * [1.3](in-depth/architecture-decision-records/1.3.md)
  * [Pre-1.3](in-depth/architecture-decision-records/pre-1.3.md)
* [Architecture](in-depth/architecture/README.md)
  * [How do VPN statistics work](in-depth/architecture/how-do-vpn-statistics-work.md)
  * [Security concepts](in-depth/architecture/security-concepts.md)
  * [MFA Architecture](in-depth/architecture/architecture.md)
* [Roadmap](in-depth/roadmap.md)
* [Release cycle](in-depth/release-cycle.md)

## For Developers

* [Contributing](for-developers/contributing.md)
* [Environment setup](for-developers/dev-env-setup/README.md)
  * [Translations (core/web)](for-developers/dev-env-setup/translations-web/README.md)
    * [Switching language](for-developers/dev-env-setup/translations-web/switching-language.md)
    * [Adding translations](for-developers/dev-env-setup/translations-web/adding-translations.md)
  * [Translations (client)](for-developers/dev-env-setup/translations-client/README.md)
    * [Adding translations](for-developers/dev-env-setup/translations-client/adding-translations.md)
