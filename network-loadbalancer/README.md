## Introduction


This Installation contains below directory structure :

```
jhub                             ## Jupyterhub Chart
istio-ingress                    ## Istio Ingress helm chart for installing istio-ingress-gateway
kind-config.yaml                 ## Cluster configuration without any special node-labels or port-mappings
istio-kind-profile.yaml          ## Istio installation profile
metallb.yaml                     ## Metallb manifest for installing metal-loadbalancer in cluster.

```

<br>

**jhub :** 

jupyterhub chart from [zero to Jhub](https://z2jh.jupyter.org/) this also has minimal changes to `values.yaml` to get the chart working locally with ingress.

<br>

**kind-config.yaml:**

 configuration for kind cluster.

 <br>

**istio-kind-profile.yaml:**

The istio profile changes which are needed for proper installtion of istio.

<br>

**istio-ingress**

The istio ingress gateway chart to install the istio-ingress gateway.

<br>


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
kubectl create ns j-hub
```

* Next install the `istio` service mesh.

```
istioctl install --set profile=default --set meshConfig.accessLogFile=/dev/stdout --set values.global.logging.level=debug
```

OR you can use the config file

```
istioctl install -f istio-kind-profile.yaml
```

* Next label the j-hub namespace with istio-injection=enabled

```
k label ns j-hub istio-injection=enabled
```

* Next install the jhub-1 helm chart in corresponding namespace.

```
helm install jhub -n j-hub ./jhub
```

* Next create a privateKey/certificate pair (self-singed certs would work for POC)

```
mkdir keys
cd keys
openssl req -newkey rsa:2048 -nodes -keyout private.key -x509 -days 365 -out cert.crt

```

* Next create a `tls` secret using privateKey/cert generated in last step, make sure it's created in istio-system namespace and with correct name.

```
kubectl create secret tls istio-ingress --key private.key --cert cert.crt -n istio-system
```

* Next install the istio-ingress helm chart in corresponding namespace.

```
helm install istio-ingress -n j-hub ./istio-ingress
```


* The Jupyterhub should now be available on `https://localhost:443/hub/`



## Deleting resource

* First delete the helm charts

```
helm delete jhub -n j-hub
helm delete istio-ingress -n j-hub
```

* Next the secret created 

```
k delete secret istio-ingress -n istio-system
```

* Finally the istio installation

```
istioctl uninstall --purge -y
```

* Very last the namespaces

```
k delete ns istio-system
k delete ns j-hub
```

* Atlast the cluster itself

```
kind delete cluster --name=cluster-2
```