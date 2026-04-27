---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/external-openid-providers/custom
---

# Custom

{% hint style="warning" %}
Defguard supports custom providers that allow a **code** response type in the OpenID authorization flow.
{% endhint %}

You can also configure a custom OpenID provider. The key thing here is setting up the **Base URL** correctly. This URL is used to discover all the endpoints required for the authorization flow.

The easiest way of obtaining the Base URL is to find out what is the OpenID `.well-known` URL of your provider. For example, for Google it's `https://accounts.google.com/.well-known/openid-configuration`, in this case, the Base URL would be `https://accounts.google.com` (note the lack of a trailing slash). The part starting with `/.well-known` is added automatically, so it should be omitted from the Base URL. This is explained in more detail in the [Base URL](custom.md#base-url) section.

In order to get the **Client ID** and **Client Secret** values, refer to the documentation of your custom provider of choice.

When configuring your external OpenID provider, at some point you will need to provide a callback URL, which will redirect the user back to Defguard. This URL is in form of `<DEFGUARD_URL>/auth/callback`. Replace `<DEFGUARD_URL>` with the URL under which your dashboard is accessible, e.g. `https://defguard.example.com`. If you'd like to use OpenID enrollment through proxy too, make sure to enter an additional URI in the form of `<DEFGUARD_PUBLIC_URL>/openid/callback`.

If you're having issues with your custom provider's base URL, check Defguard's (core) logs. It should say what URL it expected.
