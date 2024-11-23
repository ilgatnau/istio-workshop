# Deploy Legacy Apps

```
k create ns radoscis
k config set-context --current --namespace radoscis
k apply -f httpbin.yaml 
k apply -f sleep.yaml
k apply -f tcp-echo.yaml
``