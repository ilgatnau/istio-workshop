Exercise 3

**Objective: Configure the app mesh** (and remove previous ELBs)

Hints:
* There has been introduced some mistakes in the files in this folder, deploy as-is and troubleshoot.
* How to resolve a fake DNS name without changing /etc/hosts file: `curl -Lv --resolve:$HOSTNAME:$PORT:$IP http://$HOSTNAME`
* Check los for incorrect routes: `kubectl logs -l app=istio-ingressgateway -c istio-proxy -n <gateway_ns>`
* Use `istio analyze` to understand the mesh config

Some useful (but not always known) Istio traffic management facts (https://istio.io/latest/docs/ops/best-practices/traffic-management/):
* A VirtualService can only be fragmented this way if it is bound to a gateway. Host merging is not supported in sidecars.
* Unlike virtual service merging, destination rule merging works in both sidecars and gateways.
* Order in which objects are created matters in some cases.

