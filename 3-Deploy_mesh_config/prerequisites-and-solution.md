# References
[Find Port Numbers and accessing services](https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/#determining-the-ingress-ip-and-ports)

# Prepare environment

```bash
# Remove old service definitions
kubectl config set-context --current --namespace radoscis
kubectl delete ing httpbin-ilb-ingress
kubectl delete svc tcp-echo httpbin-ilb-svc

kubectl -n radoscis apply -f resources
```
# Apply Your solution
```bash
kubectl apply -f gateway.yaml
kubectl apply -f httpbin-vs.yaml
```

# Check connectivity
```bash
minikube tunnel
export INGRESS_NAME=istio-ingressgateway
export INGRESS_NS=istio-system

export INGRESS_HOST=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export TCP_INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')

HOST_NAME="http.test.com"
curl -sS -v -k --resolve ${HOST_NAME}:80:${INGRESS_HOST} http://${HOST_NAME}/headers

```