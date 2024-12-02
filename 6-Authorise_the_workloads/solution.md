
```bash
minikube tunnel
NS_NAME="radoscis"
# App istio-proxy logs
kubectl -n $NS_NAME logs -c istio-proxy -f $(kubectl -n $NS_NAME get pod --selector app=httpbin -o jsonpath='{.items[0].metadata.name}')
# Istio Ingress logs
kubectl -n istio-system logs -f $(kubectl -n istio-system get pod --selector app=istio-ingressgateway -o jsonpath='{.items[0].metadata.name}')

export INGRESS_NAME=istio-ingressgateway
export INGRESS_NS=istio-system

export INGRESS_HOST=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export TCP_INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')
HOST_NAME="http.test.com"


kubectl apply -f solution/authorization-policy-on-workload.yaml 

# Try without authentication header - observer output "RBAC: access denied"
curl -sS -v -k --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Get the proper Bearer token
TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/release-1.24/security/tools/jwt/samples/demo.jwt -s)

# Try with proper authentication
curl -sS -v -k --header "Authorization: Bearer $TOKEN"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Try with inproper TOKEN - observe output complaining on JWT token "Jwt is not in the form of Header.Payload.Signature with two dots and 3 section"
curl -sS -v -k --header "Authorization: Bearer deadbeef"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# As we introduced TCP rule for specific port
# Check connectivity TCP
# Tail Logs form tcp app
NS_NAME=radoscis
kubectl -n $NS_NAME logs -c istio-proxy -f $(kubectl -n $NS_NAME get pod --selector app=tcp-echo -o jsonpath='{.items[0].metadata.name}')

export INGRESS_NAME=istio-ingressgateway
export INGRESS_NS=istio-system

export INGRESS_HOST=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$INGRESS_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')

HOST_NAME="tcp.test.com"
echo "Test Port $INGRESS_PORT" | nc $INGRESS_HOST $INGRESS_PORT
curl telnet://${INGRESS_HOST}:${INGRESS_PORT} <<< "Request for 31400"

```