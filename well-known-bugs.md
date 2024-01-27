# Well known bugs

### Gateway

#### Error installing package on FreeBSD/OPNSense

We need a FreeBSD Runner in order to build correctly FreeBSD/OPNSense packages, since FPM makse incorrect packages.

Watch this issue: [https://github.com/DefGuard/deployment/issues/37](https://github.com/DefGuard/deployment/issues/37)

A workaround for FreeBSD/OPNSense packages: [https://github.com/DefGuard/deployment/issues/31](https://github.com/DefGuard/deployment/issues/31)

### Client

#### Network problems on macOS and Windows

The client desn't disconnect when the app is closed and the tunnel is still active. **Disconnect** before closing the app.

Watch the issue: [https://github.com/DefGuard/client/issues/194](https://github.com/DefGuard/client/issues/194)
