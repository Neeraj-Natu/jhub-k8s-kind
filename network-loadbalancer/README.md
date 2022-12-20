## Introduction


This Installation contains below directory structure :

```
jhub-1                           ## Jupyterhub Chart
istio-ingress                    ## Istio Ingress helm chart for installing istio-ingress-gateway
kind-config.yaml                 ## Cluster configuration without any special node-labels or port-mappings
istio-kind-profile.yaml          ## Istio installation profile
metallb.yaml                     ## Metallb manifest for installing metal-loadbalancer in cluster.

```

<br>

**jhub-1 :** 

jupyterhub chart from [zero to Jhub](https://z2jh.jupyter.org/) this also has minimal changes to `values.yaml` to get the chart working locally with ingress.

<br>

**kind-config.yaml:**

 configuration for kind cluster.

 <br>

**istio-kind-profile.yaml:**

The istio profile changes which are needed for proper installtion of istio.

**istio-ingress**

The istio ingress gateway chart to install the istio-ingress gateway.


**metallb.yaml**

Metallb manifest for installing metal-loadbalancer in cluster.

<br>

---

## Getting Started

* Make sure you have `docker-desktop`, `helm` and `istioctl` installed and running.

* Create the `k8s` cluster.

```
kind create cluster --name=cluster-2 --config=kind-config.yaml
```

* Install `metallb` following instructions from [kind-website](https://kind.sigs.k8s.io/docs/user/loadbalancer/).

|

    ```
    kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.7/config/manifests/metallb-native.yaml
    ```

    Find the available IP range :

    ```
    docker network inspect -f '{{.IPAM.Config}}' kind
    ````

    Create the right manifest :

    ```
    apiVersion: metallb.io/v1beta1
    kind: IPAddressPool
    metadata:
    name: example
    namespace: metallb-system
    spec:
    addresses:
    - <IP-RANGE>
    ---
    apiVersion: metallb.io/v1beta1
    kind: L2Advertisement
    metadata:
    name: empty
    namespace: metallb-system
    ```

    Apply the contents :

    ```
    kubectl apply -f https://kind.sigs.k8s.io/examples/loadbalancer/metallb-config.yaml
    ```

<br>

* Next create the right namespace for jupyter-Hub chart

```
kubectl create ns jhub-1
```

* Next install the `istio` service mesh.

```
istioctl install -f istio-kind-profile.yaml
```

* Next install the jhub-1 helm chart in corresponding namespace.

```
helm install jhub-1 -n jhub-1 ./jhub-1
```

* Next install the istio-ingress helm chart in corresponding namespace.

```
helm install istio-ingress -n jhub-1 ./istio-ingress
```


* The Jupyterhub should now be available on `https://localhost:443/hub/`