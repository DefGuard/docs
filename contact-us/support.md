# Community & Support

If you have any questions about Defguard or found an issue take look at these resources:

* Report:
  * a [Bug](https://github.com/DefGuard/defguard/issues/new?assignees=\&labels=bug\&projects=\&template=bug\_report.md\&title=)
  * [Feature request](https://github.com/DefGuard/defguard/issues/new?assignees=\&labels=feature\&projects=\&template=feature\_request.md\&title=)
* Community Support is also on [Matrix](https://matrix.to/#/#defguard:teonite.com)
* If you would like to have our direct / enterprise support, [you can buy it from our website - there are two options: premium & enterprise.](https://defguard.net/pricing/)

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

### Still not working, help

In order to get help on a not working VPN setup to figure out what is actually wrong, please prepare the following things:

1. **Routing table** of the **server** and **clien**t
2. **Firewall rules** of the server and client
3. Detailed information about your VPN setup - all fields (besides the keys) from the VPN configuration.
