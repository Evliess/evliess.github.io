---
layout: index
title: "kubernetes Installation"
category: kubernetes
date: 2019-07-16 15:17:55
---

# k8s Single node installation with kubeadmin

## Install kubeadmin

```
apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

## Setup network and network policy

```
# Setup calico
curl https://docs.projectcalico.org/v3.7/manifests/calico.yaml -O
POD_CIDR="<your-pod-cidr>" \
sed -i -e "s?192.168.0.0/16?$POD_CIDR?g" calico.yaml

# Init master
kubeadm init --cri-socket /var/run/dockershim.sock

# As regular user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# As root
export KUBECONFIG=/etc/kubernetes/admin.conf

# You should now deploy a pod network to the cluster.
kubectl apply -f https://docs.projectcalico.org/v3.7/manifests/calico.yaml

# Enable schedule pods on the master
kubectl taint nodes --all node-role.kubernetes.io/master-

# Enbale K8s Dashboard

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta1/aio/deploy/recommended.yaml

# Accessing the Dashboard UI

https://github.com/kubernetes/dashboard/wiki/Creating-sample-user

# Get Token
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')


http://9.30.246.163:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

## Enable Dashboard

```
kubectl get svc -n kubernetes-dashboard
type [ClusterIP --> NodePort]
[Creating sample user] (https://github.com/kubernetes/dashboard/wiki/Creating-sample-user)

1. Create Service Account
2. Create ClusterRoleBinding
3. Bearer Token
```

## Reset 

```
kubectl drain aledo1.fyre.ibm.com --delete-local-data --force --ignore-daemonsets
kubectl delete node aledo1.fyre.ibm.com
kubeadm reset
```

## Restart

```
//查看kubectl服务状态
systemctl status kubelet
//单节点安装，关闭swap
swapoff -a
//刷新服务
systemctl daemon-reload
systemctl restart kubelet

```

# Install Istio on k8s

```
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.1.7 sh -
cd istio-1.1.7
export PATH=$PWD/bin:$PATH

for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done
kubectl apply -f install/kubernetes/istio-demo.yaml

```
*Q&A*

Q|A
---|---
kubeadm [ERROR Swap]: running with swap on is not supported. Please disable swap| ```swapoff -a```


# Minikube Installation

> Environment: Ubuntu 16.04.4 LTS  X86_64bit  

## 1.1 Install Kubectl

> version v1.10.0

```shell
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/v1.10.0/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
```

## 1.2 Install Minikube

> version v0.25.2

```shell
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.25.2/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```

## 1.3 Start Minikube

> Runs the Kubernetes components on the host and not in a VM with --vm-driver=none

```shell
export MINIKUBE_WANTUPDATENOTIFICATION=false
export MINIKUBE_WANTREPORTERRORPROMPT=false
export MINIKUBE_HOME=$HOME
export CHANGE_MINIKUBE_NONE_USER=true
mkdir $HOME/.kube || true
touch $HOME/.kube/config
export KUBECONFIG=$HOME/.kube/config

minikube start --vm-driver=none
```

## 1.4 Enabling shell autocompletion (optional)

```shell
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

## 1.5 Stop Minikube
```shell
rm -rf .minikube
minikube status
```


# Reference
[Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)  
[Kubernete技术入门与实践](./images/kubernetes/kubernetes.svg)  






