## Introduction


This Installation contains below directory structure :

```
jhub                             ## Jupyterhub Chart
kind-config.yaml                 ## Cluster configuration with port-forwarding and node-label
nginx-ingress-gateway.yaml       ## nginx ingress gateway manifest to add routes 
```

<br>

**jhub :** 

jupyterhub chart from [zero to Jhub](https://z2jh.jupyter.org/) this also has minimal changes to `values.yaml` to get the chart working locally with ingress.

<br>

**kind-config.yaml:**

 configuration for kind cluster (control-plane node has been changed to have portMappings to localhost)

 <br>

**nginx-ingress-gateway.yaml:**

The nginx ingress gateway which maps all requests to proxy-public service.

<br>

---
## Pre-Requisities

* [Docker-Desktop](https://www.docker.com/products/docker-desktop/) this is needed for `kind` as it uses docker containers as nodes in cluster.

* [kubectl binary](https://kubernetes.io/docs/tasks/tools/)

* [helm binary](https://helm.sh/docs/intro/install/)

* No Proxy setup or installed.

<br>

---

## Getting Started

* Make sure you have `docker-desktop` installed and running.

* Create the `k8s` cluster.

```
kind create cluster --name=cluster-1 --config=kind-config.yaml
```

* Install nginx backend following instructions from [kind-website](https://kind.sigs.k8s.io/docs/user/ingress/), this installs nginx as a `NodePort` service instead of `Loadbalancer`, this can be seen in `ingress-nginx` namespace.

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

* Next create the right namespace for jupyter-Hub chart

```
kubectl create ns j-hub
```

* Next install the `nginx-ingress-gateway`.

```
kubectl apply -f nginx-ingress-gateway.yaml -n j-hub
```

* Next install the jhub-1 helm chart in corresponding namespace.

```
helm install jhub -n j-hub ./jhub
```

* The Jupyterhub should now be available on `http://localhost:8080/hub/`