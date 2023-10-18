---
description: https://github.com/DefGuard/YubiKey-Provision
---

# YubiKey Provisioning

## Overview

Our provisioning client allows you to easily populate the OpenPGP application on a YubiKey, and share its public information inside Defguard.

It's completely safe, we are not storing private keys. Every key is provisioned inside an encapsulated session so any gpg related files are deleted right after the process ends successfully or not. Only public PGP and SSH keys are sent to Defguard so you can access them at any time.

## Installation

Currently, we provide Linux .rpm and .deb packages alongside Docker image, but provisioning clients can also be compiled and run under Windows and MacOS.

Note that if you decide to use Docker make sure your container has access to host machine devices, otherwise, you will encounter `No keys detected` error.

## Configuration

All of the available options are described in help:

```bash
yubikey-provision -h
```

### CLI options and configuration

Configuration can be provided in CLI with options, in environment variables, or via `.env` file. &#x20;

<table><thead><tr><th>Name</th><th>Description</th><th data-type="checkbox">Required</th><th>CLI option</th><th>Environment variable</th><th>Default value</th></tr></thead><tbody><tr><td>Provisioner ID</td><td>Shown in Defguard UI</td><td>true</td><td>--id</td><td>WORKER_ID</td><td>YubikeyProvisioner</td></tr><tr><td>Log level</td><td>Sets logging level</td><td>false</td><td>--log-level</td><td>LOG_LEVEL</td><td>info</td></tr><tr><td>GRPC Endpoint</td><td>Url of your Defguard instance GRPC endpoint</td><td>true</td><td>--grpc</td><td>GRPC_URL</td><td><a href="http://127.0.0.1:50055">http://127.0.0.1:50055</a></td></tr><tr><td>Authorization Token</td><td>Authorization Token found in Defguard UI on Provisioners page.</td><td>true</td><td>--token</td><td>DEFGUARD_TOKEN</td><td></td></tr><tr><td>Detection retries</td><td>How many times provisioner will check for YubiKey presence in system before abandoning the process.</td><td>false</td><td>--smartcard-retries</td><td>YUBIKEY_RETRIES</td><td>1</td></tr><tr><td>Retry interval</td><td>How long between retries provisioner will wait ( in seconds )</td><td>false</td><td>--smartcard-retry-interval</td><td>YUBIKEY_RETRY_INTERVAL</td><td>15</td></tr><tr><td>GPG debug level</td><td>Sets debug level for gpg command during gpg operations</td><td>false</td><td>--gpg-debug-level</td><td>GPG_DEBUG_LEVEL</td><td>none</td></tr></tbody></table>

## Client access token

To register a new provisioning client you will need an access token provided by your instance. You can find it in the info card on the "Provisioners" page.\


## Example of use

You can see available clients in Defguard web-application under "provisioners" tab.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

To provision the key:

1. select the user from "Users" page in Defguard web application (or go to "My Profile" if you're provisioning a key for yourself)\

2. insert a YubiKey to machine that is running the provisioner client.
3.  select "Provision YubiKey" from the actions menu for a User in list.\


    <figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>
4.  select your provisioner and click the "Provision YubiKey" button\


    <figure><img src="../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

The service will take a short moment to prepare and provision your keys. Once that's done you'll see a modal with your public keys that are now stored in Defguard. If the process fails for some reason you will see a short error reason returned by the provisioner.\


<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

## Common problems

#### YubiKey is not detected by the client

If the client will not detect your YubiKey, it may work if you unplug and plug it back into your machine. If you are running on Linux, try to restart the pcscd service. If you are using docker image, make sure the container has access to your host devices.
