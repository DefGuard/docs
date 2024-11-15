# Executing custom gateway commands

defguard gateway has ability to execute custom commands before and after the WireGuard tunnel us up or down.

{% hint style="warning" %}
If you want to run a shell script, you should pass it's path to your shell, for example:&#x20;

`/bin/sh -c /path/to/script`
{% endhint %}

You can use this functionality in various ways:

#### ENV Variables

* `PRE_UP` - Command to run before bringing up the interface.&#x20;
* `POST_UP` - Command to run after bringing up the interface.
* `PRE_DOWN` - Command to run before bringing down the interface.
* `POST_DOWN` - Command to run after bringing down the interface.

#### Command line arguments

* `--pre-up` - Command to run before bringing up the interface.&#x20;
* `--post-up` - Command to run after bringing up the interface.
* `--pre-down` - Command to run before bringing down the interface.
* `--post-down` - Command to run after bringing down the interface.

#### /etc/defguard/gateway.toml - configuration file entries

* `pre-up` - Command to run before bringing up the interface.&#x20;
* `post-up` - Command to run after bringing up the interface.
* `pre-down` - Command to run before bringing down the interface.
* `post-down` - Command to run after bringing down the interface.
