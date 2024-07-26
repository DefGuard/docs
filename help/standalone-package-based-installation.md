# Standalone Debian package based instalation

### Introduction

This guide will walk you through the process of installing and running Debian packages (.deb) for **core, gateway, proxy** services. We will cover system requirements, additional dependencies, installation steps, and examples of running the installed packages with configuration. (this guide will not cover all possible configurable things, the list of all environmental variables for packages you can find in [Configuraion](https://defguard.gitbook.io/defguard/admin-and-features/setting-up-your-instance/configuration))

#### Hardware Requirements

To run defguard components you should meet these expectations of your hardware - [Hardware requirements](https://defguard.gitbook.io/defguard/admin-and-features/setting-up-your-instance)

#### System Requirements

Before proeeding with the installation, ensure your system meets the following requirements:

* Debian-based operating system (Debian, Ubuntu, etc.)
* Administrative (sudo) privileges
* Internet connection for downloading packages

// TODO: postgres 
// TODO: gen cert

#### Additional dependencies

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

### Installation 
wget
dpkg package_name

which package_name (verify)
if problem with dep apt-get install -f

#### Core service

- wget file (insert `<version>`)
- example package with version

#### Gateway service

- wget file (insert `<version>`)
- example package with version

#### Proxy service

- wget file (insert `<version>`)
- example package with version

#### Update packages

#### Uninstall packages

### Running defguard

- tylko jako bare metal 
-- secret-key
-- ustaw host/port dla postgres o ile inne

### Final thoughts

-- podaj inne sposoby na odpalenie (docker compose, one-line script)
-- environment variables
-- development / contribution link

