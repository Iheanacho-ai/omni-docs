---
description: >-
  A guide on how to create a kubeconfig for a Kubernetes service account in
  Omni.
---

# Create a Kubeconfig for a Kubernetes Service Account

To follow this guide, you will need `omnictl` installed and configured. (If you haven't done so already, follow the [`omnictl` guide](install-and-configure-omnictl.md).) You will also need also need a cluster created withing Omni.

{% hint style="info" %}
Note that Omni also supports [Omni Service Accounts](create-an-omni-service-account.md), which provide authentication to Omni itself. Kubernetes Service Accounts are used to authenticate to a Kubernetes cluster, not Omni.
{% endhint %}

### Creating the Kubernetes Service Account Kubeconfig

To create a service account kubeconfig, run the following command:

```bash
omnictl kubeconfig --service-account -c <cluster> --user <username> <path to kubeconfig>
```

{% hint style="info" %}
&#x20;Replace `<path to kubeconfig>` with a path where the kubeconfig file should be written. Replace `<cluster>` with the name of the cluster in Omni. Replace `<username>` with any value you would like.&#x20;
{% endhint %}

This command will create a service account token with the given username and obtain a kubeconfig file for the given cluster and username.

You can now use `kubectl` with the generated kubeconfig.
