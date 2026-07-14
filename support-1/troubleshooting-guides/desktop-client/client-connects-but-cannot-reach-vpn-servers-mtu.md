# Client connects but cannot reach VPN servers (MTU)

On some networks — in particular mobile networks and carrier-grade NAT environments — the MTU (Maximum Transmission Unit) is lower than the standard 1500 bytes. When VPN packets exceed the network MTU they are dropped in transit, which produces partial connectivity or an apparent connection with no traffic flowing.

### Setting a custom MTU

The Defguard desktop client allows you to configure a custom MTU per location instance. Open the client, navigate to the location settings, and set the **MTU** field manually.

As a starting point, try values in the range of **1300–1420**. Lower the value gradually if connectivity problems persist. Values around 1380–1420 work for most networks; mobile networks sometimes require lower values such as 1280.

After applying the change, reconnect to the location for the new MTU to take effect.
