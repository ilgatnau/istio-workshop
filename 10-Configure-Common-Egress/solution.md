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