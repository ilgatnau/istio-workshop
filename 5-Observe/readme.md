
https://istio.io/latest/docs/tasks/observability/logs/access-log/#using-mesh-config

```
spec:
  meshConfig:
    accessLogFile: /dev/stdout
```

Ceck logs:

`kubectl logs -l app=httpbin -c istio-proxy -n <app_namespace>`