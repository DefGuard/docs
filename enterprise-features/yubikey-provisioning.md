# YubiKey Provisioning

## What is YubiKey?

YubiKey is a hardware based authentication key, which allow you to store your secret keys safely and enable 2FA. Once your keys are written to YubiKey they can't be restored, it's a great alternative to holding your private keys on your computer's hard drive where malicious apps can read your keys. If you want to read more about YubiKey take a look at the [official site](https://www.yubico.com/products/). YubiKey supports many protocols but we will focus on OpenPGP.

## Why use OpenPGP?

OpenPGP is very popular asymmetric cryptograhy protocol that uses two keys public and private public for signing for example if you want to sign your email message you can encrypt it with your private key and receiver can decrypt it using your publicly availbe public key.

## What is YubiBridge?

Idea of creating YubiBridge was that creating GPG keys for YubiKey  maybe a little complicated process for not technical people. That's why we created a python script which allows you to provision your YubiKey with automatically generated GPG keys. In a few simple steps.\
You can use it in two ways as a standalone cli program where you plugin your YubiKey then pass your name and email as arguments or Defguard client program and provision YubiKey from Defguard web app.

## How to use YubiBridge?

### As a Defguard client

On your left sidebar you'll see a provisioner tabs after clicking it you'll see provisioners page with all available provisioners if no provisioners was ever connected.

To start your provisioner you'll need to download our program and run it with docker-compose or as cli app.

#### Using docker-compose:

```yaml
version: "3.9"
services:
  ykdev:
    build:
      context: ./
      dockerfile: Dockerfile
    privileged: true
    environment: 
      - URL=defguard.company.net
      - WORKER_ID=JaneLaptop
      - LOG_LEVEL=DEBUG
```

**Note** you can find list of all environmental variables and arguments with explanation [here](../in-depth/environmental-variables-configuration.md).\
Here is sample docker-compose you can run it using `docker-compose up` or if you want to specify some cli arguments you can run it like: \
`docker-compose run ykdev -g defguard.company.net -s -i JanePC`&#x20;

Command above will start GRPC connection to `defguard.company.net` url with TLS enabled and your worker named `JanePC` after your worker connected you can see it on your Defguard instance after clicking on left side navigation and clicking provisioners. On this page you can only see list of workers with their status of connection and their IP address here you can also remove unused workers after clicking on settings button placed on right of worker.&#x20;

To create provision YubiKey for a user make sure your worker is connected and you plugged clean YubiKey with no keys on it. No worries if by mistake you plug wrong YubiKey with keys YubiBridge will not override existing keys.

Go to users page click settings button on user you want to create keys for then you'll see modal like this

![Provisioning modal first step](../.gitbook/assets/ProvisioningModal.png)

After selecting your button and provisioning YubiKey Job will be send to your worker if you look at workers logs you should see similiar logs:

```
2022-06-08 10:00:24 - INFO: Received job with id 2 to provision yubikey for: Jane Doe  janedoe@defguard.com
2022-06-08 10:00:45 - INFO: Keys have been transferred to card
2022-06-08 10:00:45 - INFO: Keys sent to DG
```

After quick moment Defguard will receive your public PGP key and SSH key here you can download your keys or copy them to clipboard. No worries if you lost your public keys they will be still available in your user profile.

![Successful provision modal](../.gitbook/assets/ProvisioningModalKeys.png)
