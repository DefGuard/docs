---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/support-1/how-to-submit-an-issue
---

# How to submit an issue

## Submitting an issue

To help us diagnose and resolve problems efficiently, please follow the process below when submitting an issue.

{% hint style="info" %}
If you are using the OVA VM image, to access the VM and gather logs, please see this chapter: [https://docs.defguard.net/deployment-strategies/ova#accessing-the-vm](https://docs.defguard.net/deployment-strategies/ova#accessing-the-vm)
{% endhint %}

### 1. Enable debug logging

Before reproducing the issue, set the log level of **all relevant components** to `DEBUG`.\
This ensures we capture the necessary details for troubleshooting.

Here is how you can do it in each component

* core: use `--log-level debug` option or set `DEFGUARD_LOG_LEVEL` environment variable to `debug`
* gateway: use `--log-level debug` option or set `DEFGUARD_LOG_LEVEL` environment variable to `debug`
* proxy: use `--log-level debug` option or set `DEFGUARD_PROXY_LOG_LEVEL` environment variable to `debug`
* client: use `--log-level debug` option or set `DEFGUARD_LOG_LEVEL` environment variable to `debug`
* `mobile client: select "debug" in "View Application Logs" section`

### 2. Reproduce the problem

Reproduce the issue while `DEBUG` logging is enabled.\
The logs will automatically include:

* Application version
* Operating system information

### 3. Collect logs and screenshots

After reproducing the issue, gather logs from **all relevant components**.\
Make sure to include **all of them** in your report.

For UI components, also take screenshots if the issue affects layout, design, or visual behavior.

{% hint style="warning" %}
Please include only the log entries captured around the time the issue occurred and make sure the logs contain the actual error message.\
This helps us identify the root cause without having to sift through unnecessary log data.
{% endhint %}

### 4. Provide steps to reproduce

In addition to logs, clearly describe how the issue can be reproduced. Include:

* Description of your deployment strategy
* Exact steps you took
* Expected behavior
* Actual behavior

### 5. Submit your report

Go to [Defguard issues page](https://github.com/DefGuard/defguard/issues) and open a new issue. Select appropriate category, whether it's a "Bug report", "Feature request" or a "Security vulnerability". When opening an issue, attach:

* Logs from all components
* Clear reproduction steps
* Any additional context you think may help (environment details, configuration snippets, etc.)

## Example submission

You'll find example issue submission [here](https://github.com/DefGuard/defguard/issues/1542).
