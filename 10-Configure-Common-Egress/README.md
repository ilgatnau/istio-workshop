# Instructions

- Apply manifests from excercise-1
- On cloudsdk pod try to run gcloud  eg. "gcloud storage ls --project gft-thought-machine-gen-ai"
- Observe tle istio-proxy logs on cloudsdk pod
- Observe tle istio-proxy logs on egress-gateway pod
- Take into consideration that cloudsdk pod initiates secure connection to google-apis. Gateway acts as "transparent" egress gateway to the world. Introduce necessary fixes
- Test Your changes
- After fixing what other error do U observe on egress-gateway pod ?? Introduce the fix and test again


# Helpful commands

```bash
NS_NAME="demo"
ING_NS="demo-ingress"
EGRESS_NS="demo-egress"
APP_NS="demo"
ING_SELECTOR="istio=demo-ingressgateway"
EGRESS_SELECTOR="istio=demo-egressgateway"
INGRESS_NAME=istio-ingressgateway

# On second terminal 
kubectl -n ${EGRESS_NS} logs -f $(kubectl -n ${EGRESS_NS} get pod --selector ${EGRESS_SELECTOR} -o jsonpath='{.items[0].metadata.name}')
kubectl exec -it $(kubectl get pods --selector app=cloudsdk -o jsonpath='{.items[0].metadata.name}') -- bash
gcloud storage ls --project gft-thought-machine-gen-ai
```