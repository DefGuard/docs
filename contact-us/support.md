# Community & Support

## Community Support

Community support is done - by the community as well as us (defguard authors) on our [Matrix](https://matrix.to/#/#defguard:teonite.com) - **Support** channel.

{% hint style="info" %}
Since this a community support please remember that it may take some time to get a response, as there is no-one _assigned_ for the support 24/h - especially during the weekends, when people are just off.
{% endhint %}

## Premium & Enterprise&#x20;

If you have an actuail production setup that requires proper support, please  [please go to our support page, where you can buy  premium or enterprise](https://defguard.net/pricing/) support.

## Found a bug? Need a feature?

* Here you can submit [a bug](https://github.com/DefGuard/defguard/issues/new?assignees=\&labels=bug\&projects=\&template=bug\_report.md\&title=)
* And here you can submit [a feature request](https://github.com/DefGuard/defguard/issues/new?assignees=\&labels=feature\&projects=\&template=feature\_request.md\&title=)

### Still not working, help

{% hint style="info" %}
Please remember that defguard is absolutly free, and the only way for now we have any support is that when someone just buys the [support on our website](https://defguard.net/pricing/). Please consider it...
{% endhint %}

In order to get help on a not working VPN setup to figure out what is actually wrong, please prepare the following things:

1. **itsthe the Routing table** of the **server** and **clien**t
2. **Firewall rules** of the server and client
3. Detailed information about your VPN setup - all fields (besides the keys) from the VPN configuration - [can be downloaded with support information feature](../admin-and-features/troubleshooting-guide/sending-support-info.md) - if you don't want to attach this to the isse/Matrix chat -  you can send it to us directly (there is a button to send).
4.  Logs - before submitting logs, please:

    1. Desktop Client
       1. Change in Desktop Client settings _Logging threshold_ to **DEBUG - **<mark style="color:yellow;">**you need to restart the desktop client after changing logging threshold.**</mark>
       2. Launch the client from the command line, so that you have more logs (the desktop client has it's own logs and there is a vpn service that is gathering logs just for the VPN connections and not the desktop client itself) - so in the terminal you will have desktop client logs:

    <figure><img src="../.gitbook/assets/Screenshot 2024-03-28 at 10.27.02.png" alt=""><figcaption></figcaption></figure>

    1. Gather desktop client **service log** (responsible for the connections) - that are located in folders:
       1. Mac & Linux: /var/log/defguard-service/
       2. Windows: C:\Logs\defguard-service (will be changed soon)
5. **After connecting and gathering Desktop Client and client Service logs,** gather logs: **core, proxy** and **gateway** logs - from `journalctl` from your servers.

Prepare a package of all this and submit it to the #Support channel.
