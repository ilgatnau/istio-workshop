# Exercise 3

**Objective: Expose the app through the ingress gateway**

# Context 

Previously we had our workload exposed though 2 different Load Balancer types, none of those was secured. In this exercise we will remove those entry points and instead we will route any external request through our recently created Ingress Gateway to the workloads. We do not intend to secure these endpoints yet but just enable basic routing capabilities.

Use the provided sample resources, however bear in mind these may not be functional and you'll need to find ut what is wrong with them to make the mesh work.

![Mesh config](./istio-workshop-mesh.png)

Hints:
* Deploy the VS in the app namespace, the Gateway will need to be deployed in the Gateway Namespace.
* There has been introduced some mistakes in the files in this folder, deploy as-is and troubleshoot.
* How to resolve a fake DNS name without changing /etc/hosts file: `curl -Lv --resolve:$HOSTNAME:$PORT:$IP http://$HOSTNAME`
* Check los for incorrect routes: `kubectl logs -l app=istio-ingressgateway -c istio-proxy -n <gateway_ns>`
* Use `istio analyze` to understand the mesh config
* Remember to remove previous Esternal LoadBalancers

Bonus points:
* Deploy a second instance of the workloads
* Configure v1 and v2 subsets and 80/20 traffic splitting rule

Some useful (but not always known) Istio traffic management facts (https://istio.io/latest/docs/ops/best-practices/traffic-management/):
* A VirtualService can only be fragmented if it is bound to a gateway. Host merging is not supported in sidecars.
* Unlike virtual service merging, destination rule merging works in both sidecars and gateways.
* Order in which objects are created matters in some cases.


