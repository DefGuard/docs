1. First, navigate in your Okta dashboard to "Applications" and create a new app integration here: 

<figure><img src="../../../.gitbook/assets/image-1.png" alt=""><figcaption></figcaption></figure>

2. Next, select following options like so:

<figure><img src="../../../.gitbook/assets/image-2.png" alt=""><figcaption></figcaption></figure>

3. On the next page, configure the application. Make sure to set the correct Sign-in URIs, those will take the form of `<DEFGUARD_DASHBOARD_URL>/auth/callback` (dashboard login) and `<DEFGUARD_ENROLLMENT_URL>/openid/callback` (if you want to perform new user enrollment using Okta). Replace `<DEFGUARD_DASHBOARD_URL>` and `<DEFGUARD_ENROLLMENT_URL>` with the URLs of your Defguard dashboard and enrollment page (proxy) accordingly. If you access your Defguard dashboard at e.g. `https://defguard.example.net` your redirect URI will be `https://defguard.example.net/auth/callback`.


<figure><img src="../../../.gitbook/assets/image-6.png" alt=""><figcaption></figcaption></figure>

4. Next, select the assignment according to your needs, we will select the option that allows every directory member to login:

<figure><img src="../../../.gitbook/assets/image-4.png" alt=""><figcaption></figcaption></figure>

5. Now, copy your client ID and secret, as you will need to paste it in your Defguard's settings.

<figure><img src="../../../.gitbook/assets/image-7.png" alt=""><figcaption></figcaption></figure>

6. Go to your Defguard settings, and fill all the required information, pasting the Client ID and Client secret from Okta:
<figure><img src="../../../.gitbook/assets/image-10.png" alt=""><figcaption></figcaption></figure>

The base URL will be based on your Okta domain. In the case of this example, the `-admin` part of the URL had to be additionally removed. To additionally verify if your Base URL is correct, you can navigate to `<YOUR_OKTA_DOMAIN>/.well-known/openid-configuration`. The issuer field here should be the same as the Base URL.

