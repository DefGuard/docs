# OpenID Connect

## What is OpenID Connect?

OpenID Connect is identity layer built on top of OAuth2 it allows third party applications to get basic information about your profile and verify your identity. It's purpose is to give you one login for multiple sites. You're probably familiar with it if you used **Login with Google**. For example if you click Login with Google you'll be redirected to Google page with verify form that you allow some website to get information from your profile for example: email, name, etc.

## How Defguard implements OpenID?

As an identity provider one of our core features is Login with Defguard which allows you to log into other websites using your Defguard account so you don't have to care about multiple passwords and leaks. At this point you may have concern and ask is it safe? Yes, it's completely safe cause all information third party app will receive is the information that you allowed on redirect page. These information then are sent to third party app as IDToken which is basically JSON Web Token with additional claims like first name or email. Your password isn't send in any step of this.

## How to add OpenID client app?

Navigate to OpenID Apps on the left side navigation then click Add new button.



