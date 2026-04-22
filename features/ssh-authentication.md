---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/ssh-authentication
---

# SSH Authentication

## Overview

Defguard allows configuring your servers to use public SSH keys stored in your instance's database. This is possible by using the [AuthorizedKeysCommand option](http://man.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man5/sshd_config.5#AuthorizedKeysCommand) in OpenSSH daemon configuration file.

Each user can manage their public SSH (and GPG keys) in profile:

<figure><img src="../.gitbook/assets/image (232).png" alt=""><figcaption></figcaption></figure>

API endpoint used for this is `/api/v1/ssh_authorized_keys`. It returns a list of public keys, each in a new line. It allows filtering by specifying a username, group, or combination of both.

```bash
# All keys for the user “Bob”
GET /api/v1/ssh_authorized_keys?username=bob

# All user keys in the “devops” group
GET /api/v1/ssh_authorized_keys?group=devops

# All keys belonging to the user “Bob” in the “devops” group
GET /api/v1/ssh_authorized_keys?username=bob&group=devops
```

Example:

```bash
admin > curl http://defguard.mydomain.net/api/v1/ssh_authorized_keys?group=ssh-access
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBm40yVaGA40oLf7AsNBoD2y95+dzGtNehvDfVaZ6Yg9
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAID4oi5NlaShRVXG6nZa4jqB6kO3sYeGd4Nhe5Tfr6EPm%
```

## Setup

There are no need in configuring Defguard itself (aside from adding SSH keys for users). All the steps below are performed on the server you intend to access via SSH using public keys provided by Defguard.

1. Create a group in Defguard, in this example it will be called `ssh-access`
2. Add group to a user
3. Add SSH key to a user.
4. Create `get_ssh_keys.sh` file.

Paste this example. This script fetches users SSH keys in specified group from Defguard instance.

```bash
#!/bin/sh
curl defguard.example.com/api/v1/ssh_authorized_keys?group=ssh-access
```

5. Make it executable, set correct ownership and permissions

```sh
sudo chown root:root /usr/local/bin/get_ssh_keys.sh
sudo chmod 0755 /usr/local/bin/get_ssh_keys.sh
```

6. Update OpenSSH daemon config (`/etc/ssh/sshd_config`) to include following lines

```
AuthorizedKeysCommand /usr/local/bin/get_ssh_keys.sh
AuthorizedKeysCommandUser nobody
```

7. Restart OpenSSH daemon

With this setup when a user in group `ssh-access` tries to log in with SSH to your server the script will make a `GET` request to your Defguard instance and fetch a list of keys. This list is then used to verify keys presented by the client.

### Other examples

Other script examples which can be useful in different server setups:

* only allow user `admin` SSH keys to be used in authorization process

```bash
#!/bin/sh

curl defguard.example.com/api/v1/ssh_authorized_keys?username=admin
```

* allow all users in `admin` group to log in, but only to `adminuser` account

```bash
#!/bin/sh

test $# -ne 1 -o "${1}" != 'adminuser' && exit 1

curl defguard.example.com/api/v1/ssh_authorized_keys?group=admin
```
