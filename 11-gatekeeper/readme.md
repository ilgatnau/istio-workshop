# References
[Gatekeeper Installation](https://open-policy-agent.github.io/gatekeeper/website/docs/install)

# Installation

```bash

helm repo add gatekeeper https://open-policy-agent.github.io/gatekeeper/charts
helm install gatekeeper/gatekeeper --name-template=gatekeeper --namespace gatekeeper-system --create-namespace

mkdir ~/install/gator
cd ~/install/gator
curl -L https://github.com/open-policy-agent/gatekeeper/releases/download/v3.17.1/gator-v3.17.1-linux-amd64.tar.gz -o gator.tar.gz
tar xpzf gator.tar.gz
sudo mv gator /usr/local/bin
```

# Task 1
Exempt system namespaces from Gatekeeper config

# Taks 2
Enable data replication for Namespace medatata (syncing metadata) to gatekeeper client

# Task 3
- Create following annotations on demo namespace

```yaml
hosts: |
    {
      "allowed": [
      "http.test.com",
      "tcp.test.com"
      ]    
    }
```
- Create Policy that allow for Gateway object refer only to hostnames from annotation on namespace
- Create gator test suite to test Your constraint
