# Android "Failed host lookup" error

When connecting to a location that uses an external OpenID provider, the Android app shows:

```
DioException [connection error]: The connection errored: Failed host lookup
```

<figure><img src="../../../.gitbook/assets/image (278).png" alt="" width="236"><figcaption></figcaption></figure>

This is caused by aggressive battery optimization on the Android device. When the app is in the background, the system restricts its network access, which breaks DNS resolution during the OpenID authentication flow.

### Fix

1. Go to **Settings** on your Android device.
2. Navigate to **Apps** > **App management** > **Defguard**.
3. Open **Battery usage**.
4. Enable **Allow background activity**.

The app will now retain network access while in the background during OpenID authentication.

