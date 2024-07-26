# Standalone Debian package based instalation

## Introduction

This guide will walk you through the process of installing and running Debian packages (.deb) for **core, gateway, proxy** services. We will cover system requirements, additional dependencies, installation steps, and examples of running the installed packages with configuration. (this guide will not cover all possible configurable things, the list of all environmental variables for packages you can find in [Configuraion](https://defguard.gitbook.io/defguard/admin-and-features/setting-up-your-instance/configuration))

### Hardware Requirements

To run defguard components you should meet these expectations of your hardware - [Hardware requirements](https://defguard.gitbook.io/defguard/admin-and-features/setting-up-your-instance)

### System Requirements

Before proeeding with the installation, ensure your system meets the following requirements:

* Debian-based operating system (Debian, Ubuntu, etc.)
* Administrative (sudo) privileges
* Internet connection for downloading packages

// TODO: postgres 
// TODO: gen cert

### Additional dependencies

As a good practise before you move on, please run these commands:

// TODO: add to `wget` line any dependencies if there is need 
```
sudo apt update 
sudo apt install wget
```

If there are any missing dependencies during installation, we recommend to fix them using the following command:

```
sudo apt-get install -f
```

## Installation 
wget
dpkg package_name

which package_name (verify)
if problem with dep apt-get install -f

### Core service

Navigate to [core repository release](https://github.com/DefGuard/defguard/releases) and choose version of core package that you want to obtain that has debian package and then swap `<version>` in the following command:

```
wget https://github.com/DefGuard/defguard/releases/download/<version>/defguard-<version>-x86_64-unknown-linux-gnu.deb
```

Example: 
```
wget https://github.com/DefGuard/defguard/releases/download/v0.11.0/defguard-0.11.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using dpkg: 
```
dpkg -i defguard-<version>-x86_64-unknown-linux-gnu.deb
```

Example:
```
dpkg -i defguard-0.11.0-x86_64-unknown-linux-gnu.deb
```

You can check is core installed properly:
```
root@cyprian:~# defguard -V
defguard 0.11.0
```
### Gateway service

Navigate to [gateway repository release](https://github.com/DefGuard/gateway/releases) and choose version of core package that you want to obtain that has debian package and then swap `<version>` in the following command:

```
wget https://github.com/DefGuard/gateway/releases/download/<version>/defguard-gateway_<version>_x86_64-unknown-linux-gnu.deb
```

Example: 
```
wget https://github.com/DefGuard/gateway/releases/download/v0.7.0/defguard-gateway_0.7.0_x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using dpkg: 
```
dpkg -i defguard-gateway_<version>_x86_64-unknown-linux-gnu.deb
```

Example:
```
dpkg -i defguard-gateway_0.7.0_x86_64-unknown-linux-gnu.deb
```

You can check is core installed properly:
```
root@cyprian:~# defguard-gateway -V
defguard-gateway 0.7.0
```

### Proxy service

Navigate to [proxy repository release](https://github.com/DefGuard/proxy/releases) and choose version of core package that you want to obtain that has debian package and then swap `<version>` in the following command:

```
wget https://github.com/DefGuard/proxy/releases/download/<version>>/defguard-proxy-<version>-x86_64-unknown-linux-gnu.deb
```

Example: 
```
wget https://github.com/DefGuard/proxy/releases/download/v0.5.0/defguard-proxy-0.5.0-x86_64-unknown-linux-gnu.deb
```

Once the package is downloaded, install it using dpkg: 
```
dpkg -i defguard-proxy-<version>-x86_64-unknown-linux-gnu.deb
```

Example:
```
dpkg -i defguard-proxy-0.5.0-x86_64-unknown-linux-gnu.deb
```

You can check is core installed properly:
```
root@cyprian:~# defguard-proxy -V
defguard-proxy 0.5.0
```

## Running defguard

- tylko jako bare metal 
-- secret-key
-- ustaw host/port dla postgres o ile inne

## Final thoughts

-- podaj inne sposoby na odpalenie (docker compose, one-line script)
-- environment variables
-- development / contribution link

