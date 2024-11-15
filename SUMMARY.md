# Table of contents

* [Introduction](README.md)

## User documentation (help) <a href="#help" id="help"></a>

* [Configuring VPN](help/configuring-vpn/README.md)
  * [Defguard Desktop Client](help/configuring-vpn/add-new-instance/README.md)
    * [Update instance](help/configuring-vpn/add-new-instance/update-instance.md)
  * [Other WireGuard® Clients](help/configuring-vpn/adding-wireguard-devices/README.md)
    * [Configuring manually a device for a new VPN Location](help/configuring-vpn/adding-wireguard-devices/configuring-a-device-for-a-new-vpn-location.md)
* [Password change / Reset](help/changing-your-password.md)
* [Enrollment & Onboarding](help/enrollment/README.md)
  * [With internal Defguard SSO](help/enrollment/with-internal-defguard-sso.md)
  * [With external SSO (Google/Microsoft/Custom)](help/enrollment/with-external-sso-google-microsoft-custom.md)
* [Setting up 2FA/MFA](help/setting-up-2fa-mfa.md)
* [Desktop Client](help/desktop-client.md)

## Admin & features

* [Troubleshooting Guide](admin-and-features/troubleshooting/README.md)
  * [Sending support information](admin-and-features/troubleshooting/sending-support-info.md)
  * [Client Windows installer exit codes](admin-and-features/troubleshooting-guide/windows-installer-exit-codes.md)
* [Deploying your instance](features/setting-up-your-instance/README.md)
  * [Upgrading](features/setting-up-your-instance/upgrading.md)
  * [One-line install script](features/setting-up-your-instance/one-line-install.md)
  * [Standalone package based installation](admin-and-features/setting-up-your-instance/standalone-package-based-installation.md)
  * [Docker images and tags](admin-and-features/setting-up-your-instance/docker-images-and-tags.md)
  * [Docker Compose](features/setting-up-your-instance/docker-compose.md)
  * [Kubernetes](community-features/setting-up-your-instance/kubernetes.md)
  * [Gateway](admin-and-features/setting-up-your-instance/gateway/README.md)
    * [Running gateway on MikroTik routers](admin-and-features/setting-up-your-instance/gateway/running-gateway-on-mikrotik-routers.md)
  * [Securing gRPC communication](admin-and-features/setting-up-your-instance/grpc-ssl-communication.md)
  * [OpenID RSA key](admin-and-features/setting-up-your-instance/openid-rsa-key.md)
  * [Configuration](features/setting-up-your-instance/configuration.md)
  * [Pre-production and development releases](admin-and-features/setting-up-your-instance/pre-production-and-development-releases.md)
  * [High Availability and Failover](admin-and-features/setting-up-your-instance/high-availability-and-failover.md)
  * [Health check](features/setting-up-your-instance/health-check.md)
* [Features & configuration](admin-and-features/features-and-configuration/README.md)
  * [VPN with 2FA/MFA](admin-and-features/features-and-configuration/wireguard/README.md)
    * [Create your VPN network](admin-and-features/features-and-configuration/wireguard/create-your-vpn-network.md)
    * [Network overview](admin-and-features/features-and-configuration/wireguard/network-overview.md)
    * [Executing custom gateway commands](admin-and-features/features-and-configuration/wireguard/executing-custom-gateway-commands.md)
    * [Multi-Factor Authentication (MFA/2FA)](admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/README.md)
      * [MFA Architecture](admin-and-features/features-and-configuration/wireguard/multi-factor-authentication-mfa-2fa/architecture.md)
    * [Remote desktop client configuration](admin-and-features/features-and-configuration/wireguard/remote-desktop-activation.md)
    * [DNS and domains](admin-and-features/features-and-configuration/wireguard/dns-and-domains.md)
  * [Remote user enrollment](admin-and-features/features-and-configuration/remote-user-enrollment/README.md)
    * [User onboarding after enrollment](admin-and-features/features-and-configuration/remote-user-enrollment/user-onboarding-after-enrollment.md)
  * [SSO (OpenID Connect)](admin-and-features/features-and-configuration/openid-connect/README.md)
    * [Portainer](admin-and-features/features-and-configuration/openid-connect/portainer.md)
    * [Grafana setup](admin-and-features/features-and-configuration/openid-connect/grafana-setup.md)
    * [Proxmox](admin-and-features/features-and-configuration/openid-connect/proxmox.md)
    * [Matrix / Synapse](admin-and-features/features-and-configuration/openid-connect/proxmox-1.md)
    * [Django](admin-and-features/features-and-configuration/openid-connect/django.md)
    * [MinIO](admin-and-features/features-and-configuration/openid-connect/minio.md)
    * [Vault](admin-and-features/features-and-configuration/openid-connect/vault.md)
  * [SMTP for email notifications](admin-and-features/features-and-configuration/setting-up-smtp-for-email-notifications.md)
  * [LDAP synchronization](admin-and-features/features-and-configuration/ldap-synchronization-setup/README.md)
    * [Configuration](admin-and-features/features-and-configuration/ldap-synchronization-setup/configuration.md)
    * [Settings table](admin-and-features/features-and-configuration/ldap-synchronization-setup/settings-table.md)
  * [YubiKey Provisioning](admin-and-features/features-and-configuration/yubikey-provisioning.md)
  * [Webhooks](admin-and-features/features-and-configuration/webhooks.md)
  * [Forward auth](admin-and-features/features-and-configuration/forward-auth.md)
  * [SSH Authentication](admin-and-features/features-and-configuration/ssh-authentication.md)

## Enterprise Features <a href="#enterprise" id="enterprise"></a>

* [License](enterprise/license.md)
* [Enteprise features](enterprise/all-enteprise-features/README.md)
  * [Automatic (real time) desktop client configuration & sync](enterprise/all-enteprise-features/automatic-real-time-desktop-client-configuration.md)
  * [External OpenID providers](enterprise/all-enteprise-features/external-openid-providers.md)
  * [VPN & Client behavior customization](enterprise/all-enteprise-features/behavior-customization.md)

## Tutorials

* [Step by step setting up a VPN server](tutorials/step-by-step-setting-up-a-vpn-server/README.md)
  * [Adding additional VPN locations](tutorials/step-by-step-setting-up-a-vpn-server/adding-additional-vpn-locations.md)

## In depth

* [Roadmap](features/roadmap.md)
* [Architecture](in-depth/architecture/README.md)
  * [Security concepts](in-depth/architecture/security-concepts.md)

## For Developers

* [Contributing](for-developers/contributing.md)
* [Environment setup](for-developers/dev-env-setup.md)
* [Translations ( web )](for-developers/translations-web/README.md)
  * [Switching language](for-developers/translations-web/switching-language.md)
  * [Adding translations](for-developers/translations-web/adding-translations.md)

## Contact us

* [Community & Support](contact-us/support.md)
