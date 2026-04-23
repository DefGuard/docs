# There was a network error. Can't reach Edge

The desktop client shows this error when it cannot complete a POST or GET request to the Edge (enrollment) service.

### Verify Edge is reachable

From the machine running the client, open the Edge enrollment URL in a browser or send a request manually:

```bash
curl -v https://enroll.company.com
```

If this fails, the problem is network-level: firewall rules, DNS, or the Edge service not running.

### Check Edge is running

On the server hosting Edge:

```bash
systemctl status defguard-proxy
journalctl -u defguard-proxy -n 100
```

### TLS version compatibility

In some cases the web server in front of Edge is configured to accept TLS 1.3 only. The Defguard desktop client supports up to TLS 1.2. If the server rejects TLS 1.2 connections, the client cannot connect and will show this error.

Configure your reverse proxy or web server to also accept TLS 1.2, or check your server's minimum TLS version setting.
