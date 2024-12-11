# Task 1

```bash
kubectl apply -f solutions/gk-config.yaml
```

# Task 2

```bash
kubectl apply -f solutions/sync-set.yaml
```

# Task 3

```bash
cd solutions/istioGatewayOnlyAllowedHosts/
# Run gator tests suite
gator verify .
# Apply contraint tempalate and constraint
kubectl apply -f constraint-template.yaml
kubectl apply -f constraint.yaml
# Observe gatekeeper forbids creation
kubectl apply -f tests/violation.yaml
# Observe Gateway object created
kubectl apply -f tests/no_violation.yaml


```