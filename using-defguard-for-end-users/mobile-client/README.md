# Mobile Client

### Installation

Mobile applications are available in the official app stores for [Android](https://play.google.com/store/apps/details?id=net.defguard.mobile) and [iOS](https://apps.apple.com/us/app/defguard-vpn-client/id6748068630).

### Guides

These guides explain how to use the Defguard Mobile to connect securely to VPN locations within your Defguard instance. It covers the entire process, from installation, adding new instances, connecting to locations, to managing your VPN connection settings.

* [Instance adding guide](instance-adding.md#adding-new-instance)
* [Connecting to Instance guide](instance-connect.md#connecting-to-instance)

### Automatic configuration update

When the configuration of an Instance is being updated (for e.g. enforcing MFA on a location or changing its MFA flow), mobile client **does** **not** refresh the Instance configuration automatically if the **app is opened**.\
This is an intended behaviour.

If you want to manually update your Instance perform:

* **Manual pull-to-refresh** on the instances screen.
* **Manual pull-to-refresh** on the locations screen.
* **Restart of the application** (reopening the app)
