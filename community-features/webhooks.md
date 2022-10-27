# Webhooks

## Basic idea

Basic idea of webhooks is to send user data to external systems in order to automate certain tasks like for example sending welcome email to newly created user.

## Setup

On the left side of Defguard navigation you'll find webhooks page

![New webhook form](../.gitbook/assets/webhookform.png)

On above form you'll see inputs like URL description token and triggers

* **URL** is an URL on which data will be sent after certain triggers
* **Description** short description of your webhook to remember it's use case
* **Secret token** is a token sent with request in authorization header **Note** if receiver didn't implement any token check it'll do nothing
* **Triggers** are events which will trigger the webhook

## Sample requests

Below is list of all triggering actions with their request header and sample JSON body which will be sent on URL given at webhook creation.

**Note** all requests are using `GET` method and sends data in body of request in JSON format.

### New user created

Triggered after creating user

Header with name of trigger

`X-Defguard-Event: user_created`

Body example:

```json
{
"email":"janedoe@email.pl",
"first_name":"jane",
"last_name":"doe",
"groups":[],
"is_admin":false,
"pgp_cert_id":"",
"pgp_key":"",
"phone":"123456789",
"ssh_key":"",
"username":"jdoe"
}
```

### User modified

Triggered after modifying user

Webhook will be triggered on new user deletion sample request:

Header

`X-Defguard-Event: user_modified`

Request body example:

```json
{
"email":"janedoe@email.pl",
"first_name":"jane",
"last_name":"doe",
"groups":["admin"],
"is_admin":false,
"pgp_cert_id":"",
"pgp_key":"",
"phone":"123456789",
"ssh_key":"",
"username":"jdoe"
}
```

### User Deleted

Triggered on deleting user

Header

`X-Defguard-Event: user_deleted`

Request body example:

`{ username: "jdoe"}`

### User YubiKey Provision

Triggered after successfully provisioning YubiKey

Header

`X-Defguard-Event: user_keys`

request body example:

```json
{
"email":"janedoe@email.pl",
"first_name":"jane",
"last_name":"doe",
"groups":["admin"],
"is_admin":false,
"pgp_cert_id":"",
"pgp_key":"",
"phone":"123456789",
"ssh_key":"",
"username":"jdoe"
}
```

**Note**
