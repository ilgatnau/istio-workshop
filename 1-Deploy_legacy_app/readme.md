# Exercise 1 - Deploy a non-secure legacy application

*(this is just a preparation and warm-up exercise)*

# Context

The provided manifests will deploy and expose 2 workloads (httpbin and tcp-echo) that will illustrate how these workloads can be exposed externally to the cluster without a Service Mesh.

Even though the L7 Ingress resource could be secured with TLS support it is not essential for this excersise as we will expose lately these workloads through a single Ingress gateway in Istio.

Note the sleep workload is just intended for testing purposes within the cluster.

![Legacy App](./istio-workshop-legacy.png)

Hints:
* Use your team/user name as the name of your namespace: <team_name>-ns
* Use the following commands to successfully test httpbin `curl -Lv http://$INGRESS_IP:80/headers` and tcp-echo workloads `echo "port 31400" | nc $INGRESS_IP 31400 | grep "hello" && echo 'connection succeeded' || echo 'connection rejected'`

# Resources

* GCE Ingress Controller: https://github.com/kubernetes/ingress-gce/blob/master/README.md#readme
* Kubernetes Ingress: https://kubernetes.io/docs/concepts/services-networking/ingress/


