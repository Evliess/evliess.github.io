---
layout: index
title: "MiniKube Install"
category: cloud
date: 2018-04-08 15:17:55
---

# Installation Minikube

## 1. Install Docker

> Environment: Ubuntu 16.04.4 LTS  X86_64bit  

1. First, add the GPG key for the official Docker repository to the system:  
```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

2. Add the Docker repository to APT sources:  
```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

3. Next, update the package database with the Docker packages from the newly added repo:  
```shell
sudo apt-get update
```

4. Make sure you are about to install from the Docker repo instead of the default Ubuntu 16.04 repo:  
```shell
apt-cache policy docker-ce
```

5. Finally, install Docker:  
```shell
sudo apt-get install -y docker-ce
```

6. Check that it's running:  
```shell
sudo systemctl status docker
```
Active: active (running) since Fri 2018-05-11 12:26:54 CST;

7. Install docker-compose:  
```
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose
```

## 2. Install MiniKube

### 2.1 Install Kubectl

> version v1.10.0

```
curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/v1.10.0/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
```

### 2.2 Install Minikube

> version v0.25.2

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.25.2/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```

### 2.4 Start Minikube

> Runs the Kubernetes components on the host and not in a VM with --vm-driver=none

```
export MINIKUBE_WANTUPDATENOTIFICATION=false
export MINIKUBE_WANTREPORTERRORPROMPT=false
export MINIKUBE_HOME=$HOME
export CHANGE_MINIKUBE_NONE_USER=true
mkdir $HOME/.kube || true
touch $HOME/.kube/config
export KUBECONFIG=$HOME/.kube/config

minikube start --vm-driver=kvm2
```

### 2.5 Enabling shell autocompletion (optional)

```
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```


# Reference
[Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)  
[Kubernete技术入门与实践](../public/svg/kubernete/kubernete.svg)  






