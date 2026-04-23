# Unix socket does not exist (Linux)

Error: `No such file or directory (os error 2)` when trying to connect to a location.

The socket at `/var/run/defguard.socket` is created by the `defguard-service` background service. If this service is not running, the socket does not exist and the GUI client cannot establish a connection.

### Fix

Run the following to enable and start the background service:

```bash
# Reload systemd to pick up the service file
sudo systemctl daemon-reload

# Enable the service to start on boot
sudo systemctl enable defguard-service

# Start it immediately
sudo systemctl start defguard-service
```

Verify the service is running:

```bash
systemctl status defguard-service
```

If the service fails to start, check its logs:

```bash
journalctl -u defguard-service -n 100
```
