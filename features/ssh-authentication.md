# SSH Authentication

## Overview

defguard allows you to configure SSH authentication on your servers to use public SSH keys stored in your instance's database. This is possible by using the [AuthorizedKeysCommand option](http://man.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man5/sshd\_config.5#AuthorizedKeysCommand) in OpenSSH daemon configuration file.

{% hint style="warning" %}
For the time being each user can only have one SSH key added. It can be done by [provisioning a YubiKey](yubikey-provisioning.md).&#x20;

Allowing users to add arbitrary pubkeys to their account is already on the roadmap. Once implemented the changes should not impact the guide below. &#x20;
{% endhint %}

The specific API endpoint used for this is `/api/v1/ssh_authorized_keys`. It returns a list of public keys, each in a new line. It allows you to filter you query by specifying a username, a group or a combination of both.

## Setup

There's no specific configuration to be performed in defguard itself (aside from adding SSH keys for users of course), all the steps below are performed on the server you want to SSH into using defguard-supplied public keys:

1. Add a script which fetches SSH keys from your defguard instance

```bash
#!/bin/sh

curl defguard.example.com/api/v1/ssh_authorized_keys?username="${1}"
```

2. Make it executable, set correct ownership and permissions

```sh
sudo chown root:root /usr/local/bin/get_ssh_keys.sh
sudo chmod 0755 /usr/local/bin/get_ssh_keys.sh
```

3. Update OpenSSH daemon config (`/etc/ssh/sshd_config`) to include following lines

```
AuthorizedKeysCommand /usr/local/bin/get_ssh_keys.sh
AuthorizedKeysCommandUser nobody
```

4. Restart OpenSSH daemon

With this setup when a user `someuser` tries to log in with SSH to your server the script will make a `GET` request to your defguard instance and fetch a list of keys assigned to `someuser` (if such a user exists). This list is then used to verify keys presented by the client.

### Other examples

Other script examples which can be useful in different server setups:

* only allow users in the `admin` group to log in with SSH

```bash
#!/bin/sh

curl defguard.example.com/api/v1/ssh_authorized_keys?group=admin&username="${1}"
```

* allow all users in `admin` group to log in, but only to `adminuser` account

```bash
#!/bin/sh

test $# -ne 1 -o "${1}" != 'adminuser' && exit 1

curl defguard.example.com/api/v1/ssh_authorized_keys?group=admin
```
