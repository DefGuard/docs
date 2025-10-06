# Enterprise features

Here is a list of all Enterprise features:

* [Ability to use external OIDC](../features/external-openid-providers/) (Google/Microsoft/Okta/JumpCloud/Custom) to login or create Defguard account.
  * Do Multi-Factor Authentication on selected VPN locations with External SSO on Desktop and Mobile clients (from version 1.5).
* [Two-way LDAP & Active Directory synchronization](../features/ldap-and-active-directory-integration/two-way-ldap-and-active-directory-synchronization.md)
* [Real time sync for client configurations](../features/remote-user-enrollment/automatic-real-time-desktop-client-configuration.md)! **First WireGuard client to support this feature!**
* Ability to define and enforce [Access Control List rules](../features/access-control-list/) / firewall management
* Ability to [stream the Activity & Audit logs to external SIEM systems](../features/activity-log/activity-log-streaming/)
* Ability to use [external OIDC for secure remote enrollment and Desktop client configuration](../features/external-openid-providers/external-oidc-secure-enrollment.md)
* Ability to [disable for users to manage their devices](../features/wireguard/behavior-customization.md#disable-for-users-to-manage-their-devices) (just admin will have this possibility).
* Ability to [disable for users to configure WireGuard clients other then Defguard desktop client](../features/wireguard/behavior-customization.md#disable-ability-to-configure-other-vpn-clients-then-defguard-desktop-client).
* Ability to [disable "All traffic" in the desktop client ](../features/wireguard/behavior-customization.md#disable-all-traffic-option-in-the-desktop-client)- just "predefined" traffic by admins.
* Ability to integrate with external tooling using [REST API](../features/integrations/api-tokens.md).

