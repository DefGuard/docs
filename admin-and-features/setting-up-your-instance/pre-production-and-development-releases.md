# Pre-production and development releases

To test any pre-production or development release:

### One-line install

The simplest way to test the latest dev or pre-release is to use one line installation with the apropriate argument. More on that in [the one-line install documentation](../../features/setting-up-your-instance/one-line-install.md).

### Binaries & packages

Each github repository of [core](https://github.com/DefGuard/defguard/releases), [gateway](https://github.com/DefGuard/gateway/releases), [proxy](https://github.com/DefGuard/proxy/releases) and the [client](https://github.com/DefGuard/client/releases) has **pre-preleases** available on their github release page - where you can download binaries or packages with the pre-release, eg:

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-18 at 14.51.11.png" alt=""><figcaption><p>defguard core example pre-release</p></figcaption></figure>

### Docker images

Each of the docker images for [core](https://github.com/DefGuard/defguard/pkgs/container/defguard), [gateway](https://github.com/DefGuard/gateway/pkgs/container/gateway) and [proxy](https://github.com/DefGuard/proxy/pkgs/container/defguard-proxy) have the following tags:

* `pre-release`- this tag is for the **latest pre-production release** - which also has a version `vX.Y.Z-alpha/beta/rcX` from the `main` branch
* `dev` - this tag is for the latest development release from the `dev` branch.

#### Docker compose

Please change the version or tags as stated above.





