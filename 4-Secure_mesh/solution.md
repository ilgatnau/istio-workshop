 
 ```bash
NS_NAME="demo"
ING_NS="demo-ingress"
EGRESS_NS="demo-egress"
APP_NS="demo"
ING_SELECTOR="istio=demo-ingressgateway"
INGRESS_NAME=istio-ingressgateway

kubectl create -n ${ING_NS} secret tls certificates --key=./http.test.com.key --cert=./http.test.com.crt
kubectl apply -f solution

# Check connectivity HTTP
# In second terminal
minikube tunnel
# In separate terminals
# App istio-proxy logs
kubectl -n $NS_NAME logs -c istio-proxy -f $(kubectl -n $NS_NAME get pod --selector app=httpbin -o jsonpath='{.items[0].metadata.name}')
# Istio Ingress logs
kubectl -n istio-system logs -f $(kubectl -n istio-system get pod --selector app=istio-ingressgateway -o jsonpath='{.items[0].metadata.name}')


export INGRESS_HOST=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export TCP_INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')

HOST_NAME="http.test.com"
curl -sS -v -k --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Return just HTTP_CODE

curl -s  -k --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers -o /dev/null -w "%{http_code}\n"

```


```