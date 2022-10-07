
Useful (but not always known) Istio traffic management facts (https://istio.io/latest/docs/ops/best-practices/traffic-management/):
* A VirtualService can only be fragmented this way if it is bound to a gateway. Host merging is not supported in sidecars.
* Unlike virtual service merging, destination rule merging works in both sidecars and gateways.
* Order in which objects are created matters in some case:
** TCP
** Rutes to subsets


Tips:
* How to resolve a fake DNS name without changing /etc/hosts file: `curl -Lv -HHost:http.test.com --resolve http.test.com:80:<ingress_ip>`
* Check los for incorrect routes: `kubectl logs -l app=istio-ingressgateway -c istio-proxy -n <gateway_ns>`