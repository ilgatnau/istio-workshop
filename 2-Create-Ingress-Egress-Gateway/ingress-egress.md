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
```


# Prepare IstioOperator definitions

[IstioOperator - Reference](https://istio.io/latest/docs/reference/config/istio.operator.v1alpha1/#GatewaySpec)
Example:


Create 2 files:
- istio-egress-operator.yaml
- istio-ingress-operator.yaml

For Ingress add following ports. Egress leave default
```yaml
- name: tcp
    protocol: TCP
    port: 9090
- name: http2
    protocol: TCP
    port: 80
    targetPort: 8080
- name: https
    protocol: TCP
    port: 443
    targetPort: 8443
- name: status-port
    protocol: TCP
    port: 15021
    targetPort: 15021

```

# Generate ingress manifest
istioctl manifest generate --filename istio-ingress-operator.yaml --cluster-specific > istio-ingress-deployment.yaml

# Generate egress manifest
istioctl manifest generate --filename istio-egress-operator.yaml --cluster-specific > istio-egress-deployment.yaml

kubectl apply -f istio-ingress-deployment.yaml
kubectl apply -f istio-egress-deployment.yaml

kubectl get pods -n ${ING_NS}
kubectl get pods -n ${EGRESS_NS}
```