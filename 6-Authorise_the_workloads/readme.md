
Objective: Use Authorization policies

Hint: Use this example for JWT based AuthZ https://istio.io/latest/docs/tasks/security/authentication/authn-policy/#require-a-valid-token 

```bash
NS_NAME="demo"
ING_NS="demo-ingress"
EGRESS_NS="demo-egress"
APP_NS="demo"
ING_SELECTOR="istio=demo-ingressgateway"
INGRESS_NAME=istio-ingressgateway

minikube tunnel

# App istio-proxy logs
kubectl -n $NS_NAME logs -c istio-proxy -f $(kubectl -n $NS_NAME get pod --selector app=httpbin -o jsonpath='{.items[0].metadata.name}')

# TCP app istio-proxy logs
kubectl -n $NS_NAME logs -c istio-proxy -f $(kubectl -n $NS_NAME get pod --selector app=tcp-echo -o jsonpath='{.items[0].metadata.name}')
# Istio Ingress logs
kubectl -n ${ING_NS} logs -f $(kubectl -n ${ING_NS} get pod --selector app=istio-ingressgateway -o jsonpath='{.items[0].metadata.name}')


export INGRESS_HOST=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export SECURE_INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
export TCP_INGRESS_PORT=$(kubectl -n "$ING_NS" get service "$INGRESS_NAME" -o jsonpath='{.spec.ports[?(@.name=="tcp")].port}')


kubectl apply -f default-allow-nothing-auth.yaml
kubectl apply -f authorization-policy-on-workload.yaml 
```
As we introduced TCP rule for specific port, check connectivity TCP.

Check connectivity
```bash
HOST_NAME="tcp.test.com"
echo "Test Port $INGRESS_PORT" | nc $INGRESS_HOST $TCP_INGRESS_PORT
# or
curl telnet://${INGRESS_HOST}:${TCP_INGRESS_PORT} <<< "Request for 9090"
```

Did the conneciton succeed ? Look at applied authorizaiton-policy introduce fixes


Let's play with httpbin pod

```bash
HOST_NAME="http.test.com"
# Try without authentication header - observer output "RBAC: access denied"
curl -sS -v -k --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Get the proper Bearer token
TOKEN=$(curl https://raw.githubusercontent.com/istio/istio/release-1.24/security/tools/jwt/samples/demo.jwt -s)

# Try with proper authentication
curl -sS -v -k --header "Authorization: Bearer $TOKEN"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers

# Try with inproper TOKEN - observe output complaining on JWT token "Jwt is not in the form of Header.Payload.Signature with two dots and 3 section"
curl -sS -v -k --header "Authorization: Bearer deadbeef"  --resolve ${HOST_NAME}:${SECURE_INGRESS_PORT}:${INGRESS_HOST} https://${HOST_NAME}/headers
```