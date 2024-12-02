# Deploy Legacy Apps

```bash
k create ns radoscis
k config set-context --current --namespace radoscis
k apply -f httpbin.yaml 
k apply -f sleep.yaml
k apply -f tcp-echo.yaml
```

Check deployment httpbin
```bash
SVC_NAME="httpbin-ilb-svc"
# Use tunneling
minikube -n radoscis service $SVC_NAME --url 
## On second terminal 
curl -Lv http://127.0.0.1:44837/headers

# Use minikube IP and NodePOrt
SVC_NAME="httpbin-ilb-svc"
INGRESS_IP=$(minikube ip -p istio)
SVC_PORT=$(kubectl get svc $SVC_NAME -o jsonpath='{.spec.ports[0].nodePort}')
curl -Lv http://${INGRESS_IP}:${SVC_PORT}/headers
```

Check deployment tcpecho
```bash
SVC_NAME="tcp-echo"
INGRESS_IP=$(minikube ip -p istio)
SVC_PORT=$(kubectl get svc $SVC_NAME -o jsonpath='{.spec.ports[0].nodePort}')
# Open second terminal observe logs
kubectl logs -f $(kubectl get pods --selector app=tcp-echo -o jsonpath='{.items[0].metadata.name}')
# On first terminal 
echo "port $SVC_PORT" | nc $INGRESS_IP $SVC_PORT 
```

