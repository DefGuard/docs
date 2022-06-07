# Yubikey Provisioning

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

Here is sample docker-compose you can run it using `docker-compose up`&#x20;
