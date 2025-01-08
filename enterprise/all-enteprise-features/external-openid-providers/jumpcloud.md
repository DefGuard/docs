# JumpCloud

1. Login to your JumpCloud admin account.
2. Navigate to SSO Applications\


    <figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>


3. Add a new SSO Application
4. Select "Custom" on this screen\


    <figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>


5. Select "Configure SSO with OIDC"\


    <figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>


6. Fill the app's display label in the next form.\


    <figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>


7. After finishing this configuration you will be redirected to your newly created SSO Application's settings. Go to the "SSO" tab first.\


    <figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
8. Configure as following:

    <figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

    Make sure to set the correct Redirect URI and Login URL that will reflect your Defguard's setup. If you access your Defguard dashboard at e.g. `https://defguard.example.net` your redirect URI will be `https://defguard.example.net/auth/callback` and the login URL `https://defguard.example.net/auth/login`. Additionally, if you are using a Defguard proxy to enroll users, you can also add another redirect URI in the form of `<DEFGUARD_ENROLLMENT_URL>/openid/callback`, where the `<DEFGUARD_ENROLLMENT_URL>` is the address at which your proxy enrollment page is accessible.
9. Next, select the profile scope and add an `email` user attribute mapping by hand, like so:\


    <figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

    It's important **not** to select the email standard scope checkbox, as it will automatically add a constant `email_verified` field which doesn't conform to the OpenID standard and doesn't work with Defguard. You can see the following section for more information: [#jumpcloud](./#jumpcloud "mention").
10. Click "Activate". You will be presented with a client ID and a secret. Copy both of them, as you will need to insert them in Defguard's settings.
11. Go to Defguard settings, OpenID tab, select a `Custom` provider tab and paste the copied values:\


    <figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

    Set the base URL to `https://oauth.id.jumpcloud.com/`. The display name may be whatever you want.
12. Back in JumpCloud, make sure your users have access to the SSO Application. You can enable it by navigating to the `User groups` menu and selecting the group you want to enable logging in through JumpCloud for. Only users from this group will be able to login to Defguard with JumpCloud. In this example, we will select the `All users` group, which is a dynamic group containing every user.
13. Now in the group settings menu, select the `Applications` tab and select the checkbox next to your newly created app, this will enable the app for that group. Click `Save group` when you finish.\


    <figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>


14. Now you should be able to login to Defguard with JumpCloud.
