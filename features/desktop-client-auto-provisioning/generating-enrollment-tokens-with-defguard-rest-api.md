# Generating enrollment tokens with Defguard REST API

To automate generating enrollment tokens for a large number of users you can leverage the [REST API ](../integrations/api-tokens.md)provided by your Defguard instance.

This guilde explains how to do that with the assumption that your goal is to generate enrollment tokens for all users in a specific Defguard group.

In real world scenarios this group might be synchronized with some external identity provider like in our [example Windows scripts](generating-enrollment-tokens-with-defguard-rest-api.md#example-windows-scripts).

## Step-by-step overview

Regardless of the specific scripting language or automation tool you'll be using, the general process will probably follow similar steps:

1. Fetch all members of the target group
2. Iterate over all members and generate an enrollment token for each one
3. Store the tokens to transfer them to end-user machines as [configuration files](./#provisioning-configuration-file)

## Relevant API endpoints

### GET api/v1/group/{groupname}

This endpoint retrieves detailed info about a group, including a list of usernames which can be used for generating tokens.

#### Parameters

* `groupname` - name of the target group

#### Example response

The response is a JSON object. Usernames can be found in the `members` property:

```json
{
  "is_admin": false,
  "members": [
    "user",
    "user2",
    "user3"
  ],
  "name": "name",
  "vpn_locations": [
    "location"
  ]
}
```

### POST api/v1/user/{username}/start\_enrollment

This endpoint is used to start the enrollment process for a user. It generates an enrollment token and marks the user as pending enrollment.

Please note that by default an enrollment token is valid for 24h and cannot be used later. If you expect your users to need more time, use the `token_expiration_time` option described below.

#### Parameters

* `username` - name of the target user

#### Request body

All fields are optional and can be set to `null`.&#x20;

If `send_enrollment_notification` is set to `true`, an email address must be provided. In this case a notification with an enrollment link will be sent to the specified address. If no token expiration time is specified, the default of 24 hours will be used.

`token_expration_time` is configured as a human-readable string. For example:

* 1w - one week
* 3d - three days
* 12h - twelve hours

```json
{
  "email": "user@example.com",
  "send_enrollment_notification": true,
  "token_expiration_time": "1w"
}
```

#### Example response

The response is a JSON object:

```json
{
  "enrollment_token": "NotAnActualEnrollmentToken",
  "enrollment_url": "https://enrollment.example.com"
}
```

### GET api/v1/user/{username}

Fetches user details. This can be useful if you need some other user parameter (like email address) to integrate with other parts of your environment (for example Entra ID).

#### Parameters

* `username` - name of the target user

#### Example response

The response is a JSON object:

```json
{
  "biometric_enabled_devices": [],
  "devices": [],
  "security_keys": [],
  "user": {
    "authorized_apps": [],
    "email": "mail@defguard.net",
    "email_mfa_enabled": false,
    "enrolled": true,
    "first_name": "first_name",
    "groups": [],
    "id": 2,
    "is_active": true,
    "is_admin": false,
    "last_name": "last_name",
    "ldap_pass_requires_change": false,
    "mfa_enabled": false,
    "mfa_method": "None",
    "phone": "000000000",
    "totp_enabled": false,
    "username": "username"
  }
}
```

## Example Windows scripts

We provide pre-made PowerShell scripts for generating tokens and storing them in [Active Directory](auto-provisioning-in-windows-environments/on-premise-active-directory-environments.md#generating-enrollment-tokens) or [Entra ID](auto-provisioning-in-windows-environments/entra-id-environments.md#generating-enrollment-tokens). They can be used as reference and adjusted for your specific needs.
