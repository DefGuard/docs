---
metaLinks:
  alternates:
    - https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/features/network-devices
---

# Network devices

Network devices are like regular user devices but can only be managed by admins and have access to only one network. They are designed to be used with the [Defguard CLI client](../using-defguard-for-end-users/cli-client.md).

### Adding a new network device

In order to add a new network device, navigate to the network devices menu (select it from the menu bar at the left).

While in the network device menu, click the "Add new network device" button.

<figure><img src="../.gitbook/assets/image (266).png" alt=""><figcaption></figcaption></figure>

&#x20;You will be presented with a popup prompting you to select your method of setting up the network device.

* **Defguard Command Line Client -** choose it to automatically configure your device with the [Defguard CLI client](../using-defguard-for-end-users/cli-client.md)
* **Manual WireGuard Client** - choose it if you don't want to use the Defguard CLI client. You will need to configure your network device manually with a WireGuard config file.

<figure><img src="../.gitbook/assets/image (267).png" alt=""><figcaption></figcaption></figure>

#### Using the Defguard CLI client

After selecting the first option you will be presented with the initial setup screen.

<figure><img src="../.gitbook/assets/image (269).png" alt=""><figcaption></figcaption></figure>

You can specify here the following settings:

* **Device name** - the name used to identify the device, keep it unique in regard to other network devices. This name will be displayed on the network device list,
* **Location** - the network to which the device should have access,
* **Assigned IP Address** - automatically suggested IP address, you may change it as needed,
* **Description** - the description to help you identify the device, it will be displayed in the device list.

After you've finished setting those values, proceed to the next step. You will be presented with an enrollment command. Learn more about further steps from the [CLI client documentation](../using-defguard-for-end-users/cli-client.md).

#### Using the Manual WireGuard client

The screen here is similar to that of the CLI client configuration, except for the additional public key field.

<figure><img src="../.gitbook/assets/image (272).png" alt="" width="563"><figcaption></figcaption></figure>

The fields are as follows:

* **Device name** - the name used to identify the device, keep it unique in regard to other network devices. This name will be displayed on the network device list,
* **Location** - the network to which the device should have access,
* **Assigned IP Address** - automatically suggested IP address, you may change it as needed,
* **Description** - the description to help you identify the device, it will be displayed in the device list.

If you already have a public key for your device, insert it into the public key field. Otherwise, select the option to generate the key pair.

On the next screen you will be presented with the WireGuard configuration file. Copy, download or scan it to import it to your WireGuard client.

### Displaying network device configuration and enrollment token

After you've configured your network device, you can display its enrollment token again, by interacting with the following menu:

<figure><img src="../.gitbook/assets/image (273).png" alt="" width="360"><figcaption></figcaption></figure>

* Selecting "Generate auth token" will re-generate the enrollment token and will allow you to enroll your CLI client again. Use it if you want to manually pull the newest network configuration for your client.
* Selecting the "View config" option will display the WireGuard configuration file (without the private key, as Defguard doesn't store it).
