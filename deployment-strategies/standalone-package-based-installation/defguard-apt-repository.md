---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/standalone-package-based-installation/defguard-apt-repository
---

# Defguard APT repository

APT stands for Advanced Package Tool, Debian’s package manager for installing, updating, and removing software via simple commands.

### Distribution

Defguard APT repository provides packages for **Debian 12/13**, and **Ubuntu 22.04/24.04 LTS.**\
Packages are available on the default `trixie` repository distribution.

### Adding Defguard APT repository

To add Defguard APT repository, run following commands in your terminal:

```bash
sudo apt update 
sudo apt install -y ca-certificates curl 
#Add official Defguard public GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://apt.defguard.net/defguard.asc -o /etc/apt/keyrings/defguard.asc
sudo chmod a+r /etc/apt/keyrings/defguard.asc

#Add APT repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/defguard.asc] https://apt.defguard.net/ trixie release " | \
   sudo tee /etc/apt/sources.list.d/defguard.list > /dev/null 

sudo apt update
```

Afterward running these commands, you can install and update Defguard via APT.

After new release, simply use `sudo apt update` to update repository.

### Using pre-release builds

Defguard has two separate components on one APT repository, **release** and **pre-release.** If you want to install packages from pre-release, simply change `release` to `pre-release` in the installation steps described above, or run the following line.

```sh
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/defguard.asc] https://apt.defguard.net/ trixie pre-release " | \
   sudo tee /etc/apt/sources.list.d/defguard.list > /dev/null 
   
sudo apt update
```

### Installing packages

Defguard Core:

```sh
sudo apt install defguard
```

Defguard Proxy:

```sh
sudo apt install defguard-proxy
```

Defguard Gateway:

```sh
sudo apt install defguard-gateway
```

Defguard Client:

```sh
sudo apt install defguard-client
```
