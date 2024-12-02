# References
https://waytoeasylearn.com/learn/book-details-sample-application/

# Install

## System setup

Increase WSL memory limits
```bash
C:\Users\rados\.wslconfig
[wsl2]
memory=12GB

wsl --shutdown
```

Configure systemd on WSL
```bash
/etc/wsl.conf

[boot]
systemd=true
[user]
default=radek
```

[Follow instructions to install docker](https://docs.docker.com/engine/install/ubuntu/)

Install Tools
```bash
sudo add-apt-repository ppa:rmescandon/yq
sudo apt update
sudo apt install yq -y

sudo apt-get install jq

sudo snap install --stable dyff

cd ~/install
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod u+x kubectl 
sudo mv kubectl /usr/local/bin

sudo apt-get install bash-completion
source /usr/share/bash-completion/bash_completion
echo 'alias k=kubectl' >> ~/.bashrc
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'complete -o default -F __start_kubectl k' >> ~/.bashrc
source ~/.bashrc 
```

Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
minikube start --profile istio --memory=8192 --cpus=2 --kubernetes-version=v1.26.1
# minikube addons enable ingress --profile istio
minikube config set profile istio
```

## Install Istio


```bash
mkdir ~/install
cd ~/install
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.24.0
echo "export PATH=\$PATH:$PWD/bin" >> ~/.bashrc 
export PATH=$PATH:/home/radek/install/istio-1.24.0/bin
```

Istioctl installation method profile
- default: enables components according to the default settings of the IstioOperator API. This profile is recommended for production deployments and for primary clusters in a multicluster mesh.
- demo: configuration designed to showcase Istio functionality with modest resource requirements. It is suitable to run the Bookinfo application and associated tasks.
- minimal: same as the default profile, but only the control plane components are installed. This allows you to configure the control plane and data plane components (e.g., gateways) using separate profiles.
- remote: used for configuring a remote cluster that is managed by an external control plane or by a control plane in a primary cluster of a multicluster mesh.
- empty: deploys nothing. This can be useful as a base profile for custom configuration. 
- preview: the preview profile contains features that are experimental. This is intended to explore new features coming to Istio. Stability, security, and performance are not guaranteed - use at your own risk.
- ambient: the ambient profile is designed to help you get started with [ambient mode](https://istio.io/latest/docs/ambient/)

eg. compare profiles with customization
```bash
istioctl manifest generate --set profile=demo > generated-manifest.yaml
istioctl manifest generate -f  samples/bookinfo/demo-profile-no-gateways.yaml > generated-manifest-no-gateways.yaml
dyff between generated-manifest.yaml generated-manifest-no-gateways.yaml > generated-manifests-diff.yaml
```

Install demo profile
```bash
istioctl install --set profile=demo
k -n istio-system get cm istio -o jsonpath='{.data.mesh}'
```

$ minikube tunnel
$ minikube tunnel --cleanup