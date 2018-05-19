---
layout: index
title: "Minikube Installation"
category: cloud
date: 2018-04-28 15:17:55
---

# Minikube Installation

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

## 1.5 Stop minikube
```shell
rm -rf .minikube
miniube status
```


# Reference
[Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)  
[Kubernete技术入门与实践](../public/svg/kubernete/kubernete.svg)  






