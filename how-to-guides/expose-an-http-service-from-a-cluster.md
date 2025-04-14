---
description: A guide on how to expose an HTTP service from a cluster for external access.
---

# Expose an HTTP Service from a Cluster

### Enabling Workload Service Proxying Feature <a href="#enabling-workload-service-proxying-feature" id="enabling-workload-service-proxying-feature"></a>

You first need to enable the workload service proxying feature on the cluster you want to expose Services from.

If you are creating a new cluster, you can enable the feature by checking the checkbox in the “Cluster Features” section:

<figure><img src="../.gitbook/assets/Screenshot 2024-08-07 at 10.06.49 PM.png" alt=""><figcaption></figcaption></figure>

For an existing cluster, simply check the checkbox in the features section of the cluster overview page.

If you are using cluster templates, you can enable the feature by adding the following to the cluster template YAML:

```yaml
features:
  enableWorkloadProxy: true
```

You will notice that the “Exposed Services” section will appear on the left menu for the cluster the feature is enabled on.

### Exposing a Kubernetes Service <a href="#exposing-a-kubernetes-service" id="exposing-a-kubernetes-service"></a>

Let’s install a simple Nginx deployment and service to expose it.

Create the following `nginx.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: workload-proxy-example-nginx
  namespace: default
spec:
  selector:
    matchLabels:
      app: workload-proxy-example-nginx
  template:
    metadata:
      labels:
        app: workload-proxy-example-nginx
    spec:
      containers:
        - name: workload-proxy-example-nginx
          image: nginx:stable-alpine-slim
---
apiVersion: v1
kind: Service
metadata:
  name: workload-proxy-example-nginx
  namespace: default
  annotations:
    omni-kube-service-exposer.sidero.dev/port: "50080"
    omni-kube-service-exposer.sidero.dev/label: Sample Nginx
    #omni-kube-service-exposer.sidero.dev/prefix: myservice
    omni-kube-service-exposer.sidero.dev/icon: H4sICB0B1mQAA25naW54LXN2Z3JlcG8tY29tLnN2ZwBdU8ly2zAMvfcrWPZKwiTANWM5015yyiHdDr1kNLZsa0axvKix8/cFJbvNdCRCEvEAPDxQ8/vLSydem+Op7XeVtGCkaHbLftXuNpX8Pax1kveL+UetxY9919erZiWG/k58+/kgvjb7Xonz+Qyn182RP2DZvyjx0OyaYz30x38o8dhemqP43vfdSWi9+DDnCHFuV8O2ksmY/UWKbdNutsPfz9e2OX/pL5U0wghCvqVgqrtTJbfDsL+bzUrhM0F/3MzQGDPjlHIxH9qhaxbrtmueh7d987zbtLvLfDZtz/f1sBWrSj5aD9klhVswwdfWgLNJXR+GL6sgRwSP6QmRd53yELzCCMmRShCjqyFmLOsWwCiIKS01GJOUA0qZHQUby5ZXlsAGjkv8wmuK00A+gDfxoD1DSREQOm0teBdVgOA4wqdY1i0i+AiG4lOGbFEhg7icZWJIgCMz+It1DA/hYDQXScxVjyyohpCprBt7SswylJze49htVNxQjk6xDuSXTAs12OQgUGLWMRenLj4pTsNb11SSde/uPhmbA2U5e6c3qxBiEdhTOhhO77CIwxvJ55p7NVlN1owX+xkOJhUb3M1OTuShAZpQIoK72mtcSF5bwExLoxECjsqzssgIzdMLB2IdiPViApHbsTwhH1KNkIgFHO2tTOB54pjfXu3k4QLechmK9lCGzfm9s0XbQtmWfqa4NB0Oo1lzVtUsx6wjKxtYBcKSMkJOyGzJBbYxBM0aBypZfdBRJyDCz0zNRjXZKw0D/J75KFApFvPVTt73kv/6b0Lr9bqMp/wziz8W9M/pAwQAAA==
spec:
  selector:
    app: workload-proxy-example-nginx
  ports:
    - name: http
      port: 80
      targetPort: 80
```

Apply it to the cluster:

```bash
kubectl apply -f nginx.yaml
```

Note the following annotations on the cluster:

```yaml
omni-kube-service-exposer.sidero.dev/port: "50080"
omni-kube-service-exposer.sidero.dev/label: Sample Nginx
#omni-kube-service-exposer.sidero.dev/prefix: myservice
omni-kube-service-exposer.sidero.dev/icon: H4sICB0B1mQAA25naW54LXN2Z3JlcG8tY29tLnN2ZwBdU8ly2zAMvfcrWPZKwiTANWM5015yyiHdDr1kNLZsa0axvKix8/cFJbvNdCRCEvEAPDxQ8/vLSydem+Op7XeVtGCkaHbLftXuNpX8Pax1kveL+UetxY9919erZiWG/k58+/kgvjb7Xonz+Qyn182RP2DZvyjx0OyaYz30x38o8dhemqP43vfdSWi9+DDnCHFuV8O2ksmY/UWKbdNutsPfz9e2OX/pL5U0wghCvqVgqrtTJbfDsL+bzUrhM0F/3MzQGDPjlHIxH9qhaxbrtmueh7d987zbtLvLfDZtz/f1sBWrSj5aD9klhVswwdfWgLNJXR+GL6sgRwSP6QmRd53yELzCCMmRShCjqyFmLOsWwCiIKS01GJOUA0qZHQUby5ZXlsAGjkv8wmuK00A+gDfxoD1DSREQOm0teBdVgOA4wqdY1i0i+AiG4lOGbFEhg7icZWJIgCMz+It1DA/hYDQXScxVjyyohpCprBt7SswylJze49htVNxQjk6xDuSXTAs12OQgUGLWMRenLj4pTsNb11SSde/uPhmbA2U5e6c3qxBiEdhTOhhO77CIwxvJ55p7NVlN1owX+xkOJhUb3M1OTuShAZpQIoK72mtcSF5bwExLoxECjsqzssgIzdMLB2IdiPViApHbsTwhH1KNkIgFHO2tTOB54pjfXu3k4QLechmK9lCGzfm9s0XbQtmWfqa4NB0Oo1lzVtUsx6wjKxtYBcKSMkJOyGzJBbYxBM0aBypZfdBRJyDCz0zNRjXZKw0D/J75KFApFvPVTt73kv/6b0Lr9bqMp/wziz8W9M/pAwQAAA==
```

To expose a service, **only the `omni-kube-service-exposer.sidero.dev/port` annotation is required**.

Its value **must be a port that is unused** on the nodes, such as by other exposed Services.

The annotation `omni-kube-service-exposer.sidero.dev/label` can be set to a human-friendly name to be displayed on the Omni Web left menu.

If not set, the default name of `<service-name>.<service-namespace>` will be used.

The annotation `omni-kube-service-exposer.sidero.dev/prefix` can be set to use a user-defined prefix instead of a randomly-generated alphanumeric string as a prefix in the URL.

The annotation `omni-kube-service-exposer.sidero.dev/icon` can be set to render an icon for this service on the Omni Web left menu.

If set, valid values are:

* Either a base64-encoded SVG
* Or a base64-encoded GZIP of an SVG

To encode an SVG file `icon.svg` to be used for the annotation, you can use the following command:

```bash
gzip -c icon.svg | base64
```

### Accessing the Exposed Service <a href="#accessing-the-exposed-service" id="accessing-the-exposed-service"></a>

You will notice that the Service you annotated will appear under the “Exposed Services” section in Omni Web, on the left menu when the cluster is selected.

Clicking it will open the exposed service in Omni.

The URL will either contain a randomly-generated alphanumeric prefix, or the user-defined prefix if the `omni-kube-service-exposer.sidero.dev/prefix` annotation was set.

{% hint style="info" %}
This feature only works with HTTP services. **Raw TCP or UDP are not supported**.
{% endhint %}

The services are only accessible to users **authenticated to Omni and that have at least`Reader`level access** to the cluster containing the Service.

### Troubleshooting

You can get more info and troubleshoot the exposed services by running the following command:

```bash
omnictl get exposedservices
```

You will get an output like:

```
NAMESPACE   TYPE             ID                              VERSION   PORT    LABEL    URL                                                           ERROR                                                             HAS EXPLICIT ALIAS
default     ExposedService   talos-default/nginx-1.default   2         12345   nginx    https://myservice1-omni.<omni-url>                                                                                              true
default     ExposedService   talos-default/nginx-2.default   2         12346   nginx2   https://myservice2-omni.<omni-url>                                                                                              true
default     ExposedService   talos-default/nginx-3.default   2         0                                                                              requested alias "myservice2" is already used by another service   false
```

Inspect the output to get information about the status of your exposed service.
