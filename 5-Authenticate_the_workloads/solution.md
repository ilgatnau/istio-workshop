
```bash
NS_NAME="demo"
ING_NS="demo-ingress"
EGRESS_NS="demo-egress"
APP_NS="demo"
ING_SELECTOR="istio=demo-ingressgateway"
INGRESS_NAME=istio-ingressgateway

minikube tunnel

kubectl apply -f solution/request-authentication-on-workload.yaml

# App istio-proxy logs
kubectl -n $NS_NAME logs -c istio-proxy -f $(kubectl -n $NS_NAME get pod --selector app=httpbin -o jsonpath='{.items[0].metadata.name}')
# Istio Ingress logs
kubectl -n istio-system logs -f $(kubectl -n istio-system get pod --selector app=istio-ingressgateway -o jsonpath='{.items[0].metadata.name}')


export INGRESS_HOST=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export TCP_INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')

# Requests without authentication information will be allowed
HOST_NAME="http.test.com"
curl -sS -v -k --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers
# Requests with incorrect authentication information will be discarded


curl -sS -v -k --header "Authorization: Bearer deadbeef"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

curl -s  -k --header "Authorization: Bearer deadbeef" --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers -o /dev/null -w "%{http_code}\n"

# Get the proper Bearer token
TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/release-1.24/security/tools/jwt/samples/demo.jwt -s)

curl -sS -v -k --header "Authorization: Bearer $TOKEN"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Set requirement to bring proper JWT token
kubectl apply -f solution/authorization-policy-on-workload.yaml 

# Try without authentication header - observer output "RBAC: access denied"
curl -sS -v -k --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Try with proper authentication
curl -sS -v -k --header "Authorization: Bearer $TOKEN"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Try with inproper TOKEN - observe output complaining on JWT token "Jwt is not in the form of Header.Payload.Signature with two dots and 3 section"
curl -sS -v -k --header "Authorization: Bearer deadbeef"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

```