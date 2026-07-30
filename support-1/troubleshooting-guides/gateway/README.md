# Gateway

### WireGuard network interface not configured

* Make sure Defguard Gateway has been properly adopted. If not adopted, Gateway won't configure any
  WireGuard network interfaces. After the adoption, there should be files in `/etc/defguard/certs`.

  ```sh
  # ls -l /etc/defguard/certs
  total 16
  -rw-------  1 root wheel 569 May  4 09:53 core_client_cert.pem
  -rw-------  1 root wheel 602 May  4 09:53 gateway_grpc_cert.pem
  -rw-------  1 root wheel 241 May  4 09:53 gateway_grpc_key.pem
  -rw-------  1 root wheel 655 May  4 09:53 grpc_ca_cert.pem
  ```

* Check if the correct interface name is set as `ifname` in `/etc/defguard/gateway.toml`, for
  example:

  ```ini
  ifname = "wg0"
  ```

* Check if the required network interface has been configured:

  On Linux:

  ```sh
  # ip a show dev wg0
  8: wg0: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1420 qdisc noqueue state UNKNOWN group default qlen 1000
      link/none 
  ```

  On BSD/macOS:

  ```sh
  # ifconfig wg0
  wg0: flags=10080c1<UP,RUNNING,NOARP,MULTICAST,LOWER_UP> metric 0 mtu 1420
	        options=80000<LINKSTATE>
	        inet 192.168.0.1 netmask 0xffffff00
	        groups: wg
	        nd6 options=109<PERFORMNUD,IFDISABLED,NO_DAD>
  ```

* Check if WireGuard peers are configured. This requires [wireguard-tools](https://github.com/WireGuard/wireguard-tools) package being installed.

  ```sh
  # wg show wg0
  interface: wg0
    public key: VGFrZXMgb25lIERlZmd1YXJkIHRvIHJ1bGUgdGhlbSBhbGw=
    private key: (hidden)
    listening port: 51820
  
  peer: TWF5IHRoZSBmb3JjZSBvZiBEZWZndWFyZCBiZSB3aXRoIHlvdQ==
    preshared key: (hidden)
    allowed ips: 192.168.0.2/32
    transfer: 0 B received, 90.48 KiB sent
    persistent keepalive: every 25 seconds
  ```
