# Pre-requisites

**ONLY if you run this workshop on your own k8s cluster (even minikube), do not execute this step if you're in a multi-tenant cluster (ask the coach if not sure)**

# Instructions

Run the following series of commands to download and install Istio in your cluster (in istio-system).

```bash
curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.15.1 -

kubectl create namespace istio-operator

helm install istio-operator istio-1.15.1/manifests/charts/istio-operator \
 -n istio-operator

kubectl create ns istio-system
kubectl apply -f ./mesh-config.yaml -n istio-system
```

# Resources

* Istio: https://istio.io/latest/docs/setup/getting-started/
* Istio install with Helm charts: https://istio.io/latest/docs/setup/install/helm/
* Minikube: https://istio.io/latest/docs/setup/getting-started/
* Using MeshCOnfig to enable envoy logs: https://istio.io/latest/docs/setup/install/helm/
