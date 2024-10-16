# Desktop Client

## Overview

Desktop client provides an easy way to access VPN locations of multiple Defguard instances via user-friendly UI.

## Supported Platforms

### MacOS

Has no external requirements and we have wireguard-go bundled.

### Linux

{% hint style="warning" %}
On Linux the desktop client uses `resolvconf` to manage DNS servers. On newer distributions it should be a symbolic link to `resolvectl`, more details can be found on the [troubleshooting](general-troubleshooting.md) page.
{% endhint %}

### Windows

Our desktop client has **bundled** official WireGuard client - as we use **wg.exe** to manage the WireGuard tunnels.

{% hint style="warning" %}
If you have the official WireGuard client installed - defguard client installation may fail.
{% endhint %}

## Installation

#### Debian packages, macOS, and Windows packages

You can download the latest release of the desktop client for your platform from our github :\
&#x20;[https://github.com/DefGuard/client/releases](https://github.com/DefGuard/client/releases)

#### ArchLinux

There is an [AUR package](https://aur.archlinux.org/packages/defguard-client)[: defguard-client](https://aur.archlinux.org/packages/defguard-client).

If you don't know how to install AUR packages, please follow these guidlines:

* Manual install: [https://wiki.archlinux.org/title/Arch\_User\_Repository](https://wiki.archlinux.org/title/Arch\_User\_Repository)
* Installation through PARU (AUR Helper): [https://owlhowto.com/how-to-install-paru-on-arch-linux/](https://owlhowto.com/how-to-install-paru-on-arch-linux/)

## Client update

Defguard Client regularly checks for updates and in order to do so operating system name and installed application version are sent to the Defguard update service.

This functionality can be turned off in the Client settings under Updates section so that no data is sent.

<figure><img src="../../.gitbook/assets/defguard-client-settings-updates.png" alt=""><figcaption><p>"Check for updates" setting</p></figcaption></figure>

If a new version is available, a notification with a download button will be shown near the bottom of the menu.

<figure><img src="../../.gitbook/assets/defguard-client-new-release-available.png" alt=""><figcaption><p>New Desktop Client version available for download</p></figcaption></figure>
