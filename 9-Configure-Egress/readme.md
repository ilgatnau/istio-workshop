kubectl exec $SOURCE_POD -c sleep -- curl -sS http://httpbin.org/headers

Validate internal certificates
```
istioctl pc secret -n istio-system "$(kubectl get pod -l istio=egressgateway -n istio-system -o jsonpath='{.items[0].metadata.name}')" -ojson | jq '[.dynamicActiveSecrets[] | select(.name == "default")][0].secret.tlsCertificate.certificateChain.inlineBytes' -r | base64 -d | openssl x509 -text -noout | grep 'Subject Alternative Name' -A 1
```
