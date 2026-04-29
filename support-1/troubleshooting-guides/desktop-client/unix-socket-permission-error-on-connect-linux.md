# Unix socket permission error on connect (Linux)

To secure access to the background service which manages network interfaces (which requires elevated permissions) the Unix socket (`/var/run/defguard.socket`) used for gRPC communication is only accessible for users in the `defguard` group.

This means that the user who runs the GUI client must belong to this group. An incorrect setup results in `Permission denied` errors when attempting to connect to a VPN location.

By default the official packages (deb, rpm etc) should handle creating this group and adding the user, but in case of some unexpected errors it can also be done manually by running the following shell commands:

*   check if the `defguard` group exists: \\

    ```sh
    $ getent group defguard
    defguard:x:988:some_user  # this indicated that the group exists and user some_user is a member

    ```
*   if the group does not exist (you get no lines of output for the above command) create it manually:\\

    ```sh
    $ sudo groupadd -r defguard
    ```
*   add current user to the group:\\

    ```sh
    sudo usermod -a -G groupname $USER
    ```
* for the group membership changes to take effect you now need to reboot or log out and back in
*   confirm that your user is a member of `defguard` group:\\

    ```sh
    id -nG | grep -q defguard && echo "You are a member of defguard group" || echo "You are NOT a member of defguard group"
    ```
*   verify that the socket itself has correct permissions:\\

    ```sh
    $ ls -l /var/run/defguard.socket
    srw-rw---- 1 root defguard 0 Sep 15 15:02 /var/run/defguard.socket
    ```
