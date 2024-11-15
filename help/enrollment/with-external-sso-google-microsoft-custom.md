# With external SSO (Google/Microsoft/Custom)

{% hint style="info" %}
This features is available from Defguard 1.1
{% endhint %}

In this scenario defguard is only the VPN system and enrollment is used just to obtain credentials to configure the [Defguard Desktop Client](../configuring-vpn/add-new-instance/).

Please go to the enrollment URL, like: https://enrollment.company.com and choose Enrollment:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-15 at 18.48.20.png" alt="" width="563"><figcaption></figcaption></figure>

Then all you have to do is click on your SSO login - the button name depends on the SSO you have configured:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-15 at 18.49.54 (1).png" alt=""><figcaption></figcaption></figure>

Now log in with your SSO and after successful login process, you will receive information how to configure your desktop client:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-15 at 18.52.56.png" alt=""><figcaption></figcaption></figure>

Download the [defguard desktop client](https://defguard.net/download/), and [enter _Instance URL_ and _token_ shown on this screen - more details here](../configuring-vpn/#automatic-with-defguard-client).
