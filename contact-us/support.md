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

## Things don't work, please help

Before submitting your questions to our support (Matrix, direct), here are few things:

1. If you have configured your defguard instance correctly, after connecting to the VPN you should be able from a client to ping your VPN server, for example if you have the following setup:

<figure><img src="../.gitbook/assets/Screenshot 2024-03-24 at 18.36.43.png" alt="" width="313"><figcaption><p>Example VPN server IP</p></figcaption></figure>

after connecting to VPN you should be able to ping: 10.1.1.1

2. If you are not able to ping the VPN server the most common problem is that you have choosen a network that may be in conflict with your other networks (router, ...). To examine your routing use on Mac and Linux `netstat -rn` command. Let's look at the example from above, the VPN network is: 10.1.1.0/24, let's look at the network route tabile:

```
root# netstat -rn                                                                                                                                          ✔  18:40:46  
Routing tables

Internet:
Destination        Gateway            Flags               Netif Expire
default            10.123.123.1.      UGScg                 en0
10.10.0/24         link#13            UCS                   en0      !
10.0.0.0/8         link#13            UCS                   en0      !
10.1.1.0/24        10.1.1.1.          UHLWI                 utun6  1007
```

In the example above you can see that the whole 10.0.0.0 network (with mask 255.0.0.0 eg /8) is routed through default device en0.

{% hint style="danger" %}
Because of the main routing 10.0.0.0/8 the VPN server routing the network 10.1.1.0/24 (which is included in 10.0.0.0/8) will not work.
{% endhint %}

3. Another common problem is that your server on which the gateway is working, has some firewall rules that interfere with VPN network. Please examine carefully  `ufw` and `iptables` (even if ufw is disabled there may be `iptables` rules).
4. A user has no access to the VPN location - sometimes admins forget that they **change the VPN settings and change a group that is allowed to access the VPN location**. If the user is not a part of that group which VPN location is configured to access:

<figure><img src="../.gitbook/assets/Screenshot 2024-03-24 at 19.44.57.png" alt=""><figcaption></figcaption></figure>

will not be able to connect.

{% hint style="danger" %}
In this scenario the user **has VPN Location** in the client since previously were able to connect to this location, but after changing the settings the user needs to [Update their client configuration.](../help/configuring-vpn/add-new-instance/update-instance.md)

It's not done automatically now - since for security reasons there is a token required for obtaining the configuration by the desktop client.
{% endhint %}

### Still not working, help

{% hint style="info" %}
Please remember that defguard is absolutly free, and the only way for now we have any support is that when someone just buys the [support on our website](https://defguard.net/pricing/). Please consider it...
{% endhint %}

In order to get help on a not working VPN setup to figure out what is actually wrong, please prepare the following things:

1. **Routing table** of the **server** and **clien**t
2. **Firewall rules** of the server and client
3. Detailed information about your VPN setup - all fields (besides the keys) from the VPN configuration.
4. Logs - before submitting logs, please:
   1. Change in Desktop Client settings _Logging threshold_ to **DEBUG - **<mark style="color:yellow;">**you need to restart the desktop client after changing logging threshold.**</mark>
   2. Connect once more, and then gather logs:
      * **core, proxy** and **gateway** logs - from journalctl
      * defguard client service log - that are located in folders:
        * Mac & Linux: /var/log/defguard-service/
        * Windows: C:\Logs\defguard-service (will be changed soon)

