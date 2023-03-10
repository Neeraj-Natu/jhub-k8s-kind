# jhub-k8s-kind

This Repo contains all tools to install jupyterhub on k8s locally.
There are two flavours of `k8s` both are installed using [kind](https://kind.sigs.k8s.io/).
<br>
There is no pre-built `LoadBalancer` for any bare-metal `k8s` installation, thus we could either use port-forwarding or have a functionality that emulates creating a network load-balancer as in any cloud provider.

<br>

---
## Pre-Requisities

* [Docker-Desktop](https://www.docker.com/products/docker-desktop/) this is needed for `kind` as it uses docker containers as nodes in cluster.

* [kubectl binary.](https://kubernetes.io/docs/tasks/tools/)

* [helm binary.](https://helm.sh/docs/intro/install/)

* [istioctl binary](https://istio.io/latest/docs/setup/install/istioctl/) (needed only for network-loadbalancer type installation).

* No Proxy setup or installed.

<br>

---

### Below are the two flavours of installation:

<br>

**Flavour-1 :** 

**<p>port-forwarding<br>**
Here we forward certain port from the kind-k8s cluster.<br>
The entire kind cluster uses docker images as nodes,also these docker images donot allow access to the network from bare-metal.<br>

check [port-forwading](./port-forwarding/) folder for installation instructions.
</p>

---

**Flavour-2 :** 

**_Note this only works on Linux_**

**<p>Network LoadBalancer<br>**
Here we first install a network load-balancer on the kind-k8s cluster and assign it the IP address pool to expose it.<br>
[metallb](https://metallb.universe.tf/) is the network loadbalancer that is installed in the demo, this makes sure we can create a service of type `LoadBalancer` in k8s cluster.
<br>

check [network-loadbalancer](./network-loadbalancer/) folder for installation instructions.
</p>


---