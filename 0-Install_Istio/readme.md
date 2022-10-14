# Pre-requisites

**ONLY if you run this workshop on your own Cluster, do not execute this step if you're in a multi-tenant cluster (ask the coach if not sure)**

# Instructions

Run the following series of commands to download and install Istio in your cluster.

```bash
https://istio.io/latest/docs/setup/getting-started/

curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.15.1 -

kubectl create namespace istio-operator

helm install istio-operator istio-1.15.1/manifests/charts/istio-operator \
 -n istio-operator

kubectl create ns istio-system
kubectl apply -f ./mesh-config.yaml -n istio-system
```
