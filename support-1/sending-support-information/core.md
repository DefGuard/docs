# Server applications

The following support information should be attached when contacting our support team about an issue with one of our server applications: Core, Gateway, or Edge.

## 1. Anonymised configuration dump

You can download your anonymised configuration dump from the UI of the Core component. Click the ![](<../../.gitbook/assets/Screenshot 2026-07-01 at 18.10.13.png>)menu item in the main menu and then in the ![](<../../.gitbook/assets/Screenshot 2026-07-01 at 18.11.24.png>)section click the _Download Support Data_ button. Attach the file when contacting the support team.

## 2. Logs

### Log level

Logs contain more detailed diagnostic information when DEBUG-level logging is enabled. This information can be invaluable when troubleshooting issues and should be collected whenever possible before contacting support.

{% hint style="warning" %}
DEBUG logging should only be enabled during the troubleshooting process. Running applications with DEBUG logging enabled for extended periods is not recommended, as it may generate large log files and impact performance.
{% endhint %}

There are two ways to enable DEBUG logging, depending on the deployment method:

* Containerized deployments (Docker, Kubernetes, etc.): set the `DEFGUARD_LOG_LEVEL` environment variable to `debug`.
* Direct binary deployments: start the application with the `--log-level debug` command-line option.

### Log Location

The location of Defguard logs depends on how the application is deployed.

* Containerized deployments (Docker, Kubernetes, etc.): logs are written to the standard output (`stdout`) and standard error (`stderr`) streams. Retrieve them using your container runtime or orchestration platform (for example, `docker logs` or `kubectl logs`).
* Direct binary deployments: the location of the logs depends on how Defguard is started. If the application is managed by a service manager such as `systemd`, logs are written to the system journal and can be retrieved using `journalctl`. If the application is started manually, logs are written to the standard output of the process. If your deployment redirects the output to a file or another logging system, retrieve the logs from the configured destination. Here are examples on retireving logs using `journalctl`  for [Core](../../deployment-strategies/standalone-package-based-installation/core.md#logs), [Gateway](../../deployment-strategies/standalone-package-based-installation/gateway.md#logs), [Edge](../../deployment-strategies/standalone-package-based-installation/edge.md#logs).

