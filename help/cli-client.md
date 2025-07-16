# CLI Client

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
* Defguard proxy running and accessible from the machine the CLI will be installed on
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

### Usage

#### Defguard core setup

Defguard CLI works only with [network devices](../admin-and-features/network-devices.md), so to use it, you will need to first add a new network device. Refer to the network device documentation to learn more.

After you've configured your network device on Defguard core, you will be presented with the following command:

```bash
dg enroll -u <ENROLLMENT_URL> -t <TOKEN>
```

Copy the command and proceed with [enrollment](cli-client.md#enrollmen).

#### Enrollment

Execute the command obtained in the previous step to configure Defguard CLI on the machine of your choice. The enrollment command will pull all the information required to establish a connection from your Defguard instance (through the Defguard proxy, so make sure it can be accessed) and will save it in a configuration file. Run the `enroll` command only when you need to retrieve your network configuration and apply it to the CLI Client. If you have access to the enterprise features, the CLI should automatically handle this when running.

#### Connecting

After completing the enrollment, you can connect to the given network by running the following command as root:

```bash
dg
```

After executing the command you should see a message stating that you have been connected to your network of choice.

#### Automatic config fetching (polling)

If you have access to the enterprise features, CLI will periodically fetch the latest network config and apply it if it has changed. This is useful because when you edit your network configuration in Defguard core, you won't have to manually re-configure every network device.

#### Running in the background (service)

After installing the CLI, a systemd service will be automatically setup. The service won't be running at first as the manual [enrollment](cli-client.md#enrollment) is needed beforehand. After you've completed the enrollment, you can start the service, e.g. by doing:

```bash
systemctl start dg
```

You can configure the service and set the log verbosity by editing `/etc/defguard/dg.conf`.

### Debugging and troubleshooting

It may be easier to identify a problem by passing one of the following flags, which control the logging verbosity level:

```bash
--debug 
--verbose
```

Those flags can be passed to any command to display more detailed information about the given process.

#### Common issues and messages

```
Specified IFLA_INET6_STATS NLA attribute holds more(most likely new kernel) data which is unknown to netlink-packet-route crate
```

This shouldn't affect anything and can be ignored in most cases.
