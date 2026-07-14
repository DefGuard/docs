# Split Tunnel Configuration

To configure split tunnel routing, you need to add the networks that should always route through your VPN connection to the[ Allowed IPs configuration in the VPN Location](./#allowed-ips). You can do this either in the wizard during the location configuration, or by navigating to Locations, clicking on the "..." menu, and selecting "Edit":



<figure><img src="../../../.gitbook/assets/Screenshot 2026-05-22 at 21.53.11.png" alt="" width="302"><figcaption></figcaption></figure>

And then define the networks in Allowed IPs:

<figure><img src="../../../.gitbook/assets/Screenshot 2026-05-22 at 21.52.17.png" alt=""><figcaption></figcaption></figure>

Now any time a user connects to VPN with Predefined Traffic option:

<figure><img src="../../../.gitbook/assets/Screenshot 2026-05-22 at 21.58.24.png" alt="" width="563"><figcaption></figcaption></figure>

traffic to those networks will go through the VPN, and all other traffic will use the user's default router or internet connection.
