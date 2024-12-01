?(@.metadata.name=~/^test$/)]

# References
[Using Istio Revisions](https://istio.io/latest/blog/2021/revision-tags/)
[Istio Sidecar Injection](https://istio.io/latest/docs/setup/additional-setup/sidecar-injection/)
[Install Ingress](https://istio.io/latest/docs/setup/additional-setup/gateway/)
```bash
ISTIO_REV=$(kubectl -n istio-system get cm istio -o jsonpath='{.metadata.labels.istio\.io\/rev}')
kubectl create ns radoscis-ingress
kubectl create ns radoscis-egress
kubectl label namespace radoscis-ingress istio.io/rev=$ISTIO_REV
kubectl label namespace radoscis-egress istio.io/rev=$ISTIO_REV
kubectl label namespace radoscis istio.io/rev=$ISTIO_REV
kubectl -n radoscis delete pods --all
kubectl -n radoscis get pods

# Edit istio-ingress-deployment
# Set Your namespace and selctor label
istioctl manifest generate --filename istio-ingress-operator.yaml --cluster-specific > istio-ingress-deployment.yaml
istioctl manifest generate --filename istio-egress-operator.yaml --cluster-specific > istio-egress-deployment.yaml

kubectl apply -f istio-ingress-deployment.yaml
kubectl apply -f istio-egress-deployment.yaml

kubectl get pods -n radoscis-ingress
kubectl get pods -n radoscis-egress
```