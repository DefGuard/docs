# Server migration and licence transfer

This guide explains how to migrate your Defguard instance to a new server while keeping your licence valid and compliant.

## Licence information

Each Defguard licence is bound to a single machine.

When migrating to a new server, you must ensure that only one active instance is using the same licence key at any given time.

## Migration scenarios

### Migration with acceptable downtime (simpler)

This method is recommended when a short interruption in service is not a problem.

You will reuse your existing licence key, but you must first stop the old server before activating the new one to ensure licence compliance.

Steps:

1. Shut down the old Defguard server. Ensure the old instance is completely stopped before proceeding.
2. Install and configure Defguard on the new server. Use the same licence key from your previous installation.
3. Verify functionality on the new server. Confirm that users can connect and that configuration data is correctly migrated.

### Migration with zero downtime

This method allows you to migrate Defguard to a new server without interrupting service.

It requires a temporary transition licence, which lets both servers run simultaneously for testing and verification before switching over.

Steps:

1. Contact the Defguard Sales team at [sales@defguard.net](mailto:sales@defguard.net). Request a 7-day transition licence for migration purposes.
2. Install and configure Defguard on the new server using the transition licence. This allows both servers to temporarily coexist.
3. Test and verify the new setup to ensure all configurations and integrations work correctly.
4. Once verification is complete:
   1. Shut down the old Defguard server.
   2. Replace the transition licence on the new server with your original licence key.
