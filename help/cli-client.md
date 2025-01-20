# CLI Client

{% hint style="warning" %}
Documentation regarding this feature is still work in progress and incomplete.
{% endhint %}

### Downloading

Latest release page: [https://github.com/DefGuard/client/releases/tag/v1.2.0](https://github.com/DefGuard/client/releases/tag/v1.2.0)

#### Linux (AMD64)

Deb: [https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-x86\_64-v1.2.0-dg.deb](https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-x86_64-v1.2.0-dg.deb)

RPM: [https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-x86\_64-v1.2.0-dg.rpm](https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-x86_64-v1.2.0-dg.rpm)

Binary: [https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-x86\_64-v1.2.0-dg.tar.gz](https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-x86_64-v1.2.0-dg.tar.gz)

#### Linux (ARM64)

Deb: [https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-aarch64-v1.2.0-dg.deb](https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-aarch64-v1.2.0-dg.deb)

RPM: [https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-aarch64-v1.2.0-dg.rpm](https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-aarch64-v1.2.0-dg.rpm)

Binary: [https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-aarch64-v1.2.0-dg.tar.gz](https://github.com/DefGuard/client/releases/download/v1.2.0/dg-linux-aarch64-v1.2.0-dg.tar.gz)

### Requirements

* Root access on a given machine
* `resolvconf` and `ip` commands available

### Installation

Installation is straightforward. As a root, install it as any other package of a given type (deb/rpm).&#x20;

#### Deb archive

```bash
apt install ./dg-linux-x86_64-v1.2.0-dg.deb
```

#### RPM

```bash
rpm -i ./dg-linux-x86_64-v1.2.0-dg.rpm
```

#### Post install

After installing the CLI, you should gain access to the `dg` command and a new `dg` service should've been created. You can interact with the client using the `dg` command alone or use the service to run it in background. You can test if the installation succeeded by trying to print the command's help:

```bash
dg --help
```

