# jhub-k8s-kind

Installation and using basic Jupyterhub on k8s.
k8s is installed on a mac-os using kind.

jhub-1 : jupyterhub chart from https://z2jh.jupyter.org/
kind-config.yaml: configuration for kind cluster (control-plane node has been changed to have portMappings to localhost)

nginx-ingress-gateway.yaml: the nginx ingress gateway which maps all requests to proxy-public service.
this also needs installation of nginx as a NodePort service instead of load balancer.
for instructions to install nginx as NodePort check : https://kind.sigs.k8s.io/docs/user/ingress/

the one line command to install nginx as NodePort is :

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
