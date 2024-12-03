# References
[Using Istio Revisions](https://istio.io/latest/blog/2021/revision-tags/)
[Istio Sidecar Injection](https://istio.io/latest/docs/setup/additional-setup/sidecar-injection/)
[Install Ingress](https://istio.io/latest/docs/setup/additional-setup/gateway/)

```bash
ISTIO_REV=$(kubectl -n istio-system get cm istio -o jsonpath='{.metadata.labels.istio\.io\/rev}')
ING_NS="demo-ingress"
EGRESS_NS="demo-egress"
APP_NS="demo"
kubectl create ns ${ING_NS}
kubectl create ns ${EGRESS_NS}
kubectl label namespace ${ING_NS} istio.io/rev=$ISTIO_REV
kubectl label namespace ${EGRESS_NS} istio.io/rev=$ISTIO_REV
kubectl label namespace ${APP_NS} istio.io/rev=$ISTIO_REV
kubectl -n ${APP_NS} delete pods --all
kubectl -n ${APP_NS} get pods

# Set correct namespace and label selectors in istio-ingress-operator.yaml
# Generate ingress manifest
istioctl manifest generate --filename istio-ingress-operator.yaml --cluster-specific > istio-ingress-deployment.yaml

# Set correct namespace label selectors in istio-egress-operator.yaml
# Generate egress manifest
istioctl manifest generate --filename istio-egress-operator.yaml --cluster-specific > istio-egress-deployment.yaml

kubectl apply -f istio-ingress-deployment.yaml
kubectl apply -f istio-egress-deployment.yaml

kubectl get pods -n ${ING_NS}
kubectl get pods -n ${EGRESS_NS}
```