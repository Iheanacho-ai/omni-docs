---
description: A guide on installing and configuring omnictl for Omni.
---

# Install and Configure Omnictl

## 1. Installation

Pick one of the installation methods below.

### a. Recommended installation

To install `omnictl`and the kubectl `oidc-login`plugin automatically on macOS and Linux you can use the provided Hombrew formula.

```
brew install siderolabs/tap/sidero-tools
```

This will install `talosctl`, `omnictl`, and `kube-oidc_login`and keep them updated.

### b. Manual installation

Download `omnictl` from the Omni dashboard.

{% hint style="info" %}
The downloaded binary will be named according the the selected platform and architecture (e.g. `omnictl-linux-amd64`). It is left as an exercise to the user to move the downloaded binary into your `PATH` and make it executable. This guide assumes the downloaded binary has been renamed `omnictl`.

For example, to do so on a mac:

```bash
mv omnictl-darwin-arm64 omnictl
chmod +x omnictl
mv omnictl /usr/local/bin
```
{% endhint %}

## 2. Configuration File

Download `omniconfig` from the Omni dashboard.

Omnictl follows the [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/latest/) for its configuration files location.

Omni configuration is stored under `$XDG_CONFIG_HOME/omni/config`. This means, the correct configuration file locations for `omniconfig` are the following:

<table><thead><tr><th width="119.21484375">OS</th><th>Omniconfig Path</th></tr></thead><tbody><tr><td>Linux</td><td><code>~/.config/omni/config</code></td></tr><tr><td>MacOS</td><td><code>~/Library/Application Support/omni/config</code></td></tr><tr><td>Windows</td><td><code>%LOCALAPPDATA%\omni\config</code>, e.g., <code>C:\Users\&#x3C;USER>\AppData\Local\omni\config</code></td></tr></tbody></table>

Add the downloaded `omniconfig.yaml` to the default location for your OS to use it with `omnictl`:

```bash
cp omniconfig.yaml <LOCATION_FOR_YOUR_OS_ABOVE>
```

If you would like to merge the `omniconfig.yaml` with an existing configuration, use the following command:

```bash
omnictl config merge ./omniconfig.yaml
```

List the contexts to verify that the `omniconfig` was added:

```bash
$ omnictl config contexts
CURRENT   NAME         URL
          ...
          example      https://example.omni.siderolabs.io/
          ...
```

Run `omnictl` for the first time to perform initial authentication using a web browser:

```bash
omnictl get clusters
```

If the browser window does not open automatically, it can be opened manually by copying and pasting the URL into a web browser:

```bash
BROWSER=echo omnictl get clusters
```
