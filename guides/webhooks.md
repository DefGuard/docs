# Webhooks

## Basic idea

Basic idea of webhooks is to send user data to external systems in order to automate certain tasks like for example sending welcome email to newly created user

## Setup

On the left side of defguard navigation you'll find webhooks page

![New webhook form](../.gitbook/assets/webhookform.png)

On above form you'll se inputs like url description token and triggers

**Url** is an url on which data will be sent after certain triggers

**Description** short description of your webhook in case you&#x20;

**Secret token** is a token sent with request in header as authorization note if receiver didn't   implement any token check it'll do nothing&#x20;

&#x20;**Triggers** are events which will trigger the webhook



## Sample request

### New user created&#x20;

Webhook will be triggered on new user creation sample request:&#x20;

Header with action named&#x20;

&#x20;`X-Defguard-Event: user_created`

Body example:

```json
{
"email":"janedoe@email.pl",
"first_name":"jane",
"groups":[],
"is_admin":false,
"last_name":"doe",
"pgp_cert_id":"",
"pgp_key":"",
"phone":"123456789",
"ssh_key":"",
"username":"jdoe"
}
```

