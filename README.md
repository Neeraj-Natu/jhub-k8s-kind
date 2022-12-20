# jhub-k8s-kind

This Repo has artifacts to install jupyterhub on k8s locally.
There are two flavours of k8s both are installed using [kind](https://kind.sigs.k8s.io/).
As for on bare-metal there's no pre-built `LoadBalancer` thus we can either use port-forwarding (flavour-1)
or use `metallb` which is the solution for creating network-loadbalancer locally.

