---
description: A guide on how to install talosctl.
---

# Install talosctl

### Recommended

The client can be installed and updated via the [Homebrew package manager](https://brew.sh/) for macOS and Linux. You will need to install `brew` and then you can install `talosctl` from the Sidero Labs tap.

```bash
brew install siderolabs/tap/sidero-tools
```

This will also keep your version of `talosctl` up to date with new releases. This homebrew tap also has formulae for `omnictl` if you need to install that package.

### Manual installation

You can use a convenience script to install the current, latest version of \`talosctl\`. This will not install `omnictl`or [`kubelogin`](https://github.com/int128/kubelogin)needed for Omni management.

```
curl -sL https://talos.dev/install | sh
```

You now have `talosctl` installed.

> Please note that because Omni manages the state of the Talos nodes, and protects the security of the Kubernetes and Talos credentials, some `talosctl` commands (such as `talosctl reset`) will return `PermissionDenied` on Omni managed clusters - such operations must be done through the Omni UI or API calls.

### Windows installation

Windows binaries can be downloaded from GitHub releases for Talos and Omni.

* [Talos releases](https://github.com/siderolabs/talos/releases)
* [Omni releases](https://github.com/siderolabs/omni/releases)

There is also a community managed [winget package for talosctl](https://winget.run/pkg/Sidero/talosctl) available.
