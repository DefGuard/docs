# Defguard APT repository

APT stands for Advanced Package Tool, Debian’s package manager for installing, updating, and removing software via simple commands.

### Distribution

The Defguard APT repository provides packages for **Debian 12/13** and **Ubuntu 22.04/24.04 LTS.**

{% hint style="warning" %}
On **Debian 12** and **Ubuntu 22.04** you have to use the `bookworm` distribution due to a known issue with `GLIBC_2.39` .
{% endhint %}

### Adding the Defguard APT repository

To add the Defguard APT repository, run the following commands in your terminal:

```sh
sudo apt update 
sudo apt install -y ca-certificates curl 
#Add official Defguard public GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://apt.defguard.net/defguard.asc -o /etc/apt/keyrings/defguard.asc
sudo chmod a+r /etc/apt/keyrings/defguard.asc

#Add APT repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/defguard.asc] https://apt.defguard.net/ trixie release-2.0" | \
   sudo tee /etc/apt/sources.list.d/defguard.list > /dev/null 

sudo apt update
```

Or to use the `bookworm` distribution (on Debian 12 or Ubuntu 22.04):

```sh
sudo apt update 
sudo apt install -y ca-certificates curl 
#Add official Defguard public GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://apt.defguard.net/defguard.asc -o /etc/apt/keyrings/defguard.asc
sudo chmod a+r /etc/apt/keyrings/defguard.asc

#Add APT repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/defguard.asc] https://apt.defguard.net/ bookworm release-2.0" | \
   sudo tee /etc/apt/sources.list.d/defguard.list > /dev/null 

sudo apt update
```

After running these commands, you can install and update Defguard using `apt`.

After a new release, simply use `sudo apt update` to update the repository.

### Using alternative components

The Defguard APT repository has four components:

* **release**: for stable releases of v1.x packages
* **pre-release**: for pre-release versions of v1.x packages
* **release-2.0**: for stable releases of v2.0 packages
* **pre-release-2.0**: for pre-release versions of v2.0 packages

For example, if you want to install packages from `pre-release`, simply change `release` to `pre-release` in the installation steps described above, or run the following commands:

```sh
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/defguard.asc] https://apt.defguard.net/ trixie pre-release" | \
   sudo tee /etc/apt/sources.list.d/defguard.list > /dev/null 
   
sudo apt update
```

### Installing packages

Defguard Core:

```sh
sudo apt install defguard
```

Defguard Edge:

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
