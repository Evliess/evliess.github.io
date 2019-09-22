---
layout: index
title: "Istio Installation"
category: kubernetes
date: 2018-04-28 15:17:55
---

# 1. Minikube Installation

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
minikube start --vm-driver=none \
--extra-config=controller-manager.ClusterSigningCertFile="/var/lib/localkube/certs/ca.crt" \
--extra-config=controller-manager.ClusterSigningKeyFile="/var/lib/localkube/certs/ca.key" \
--extra-config=apiserver.Admission.PluginNames=NamespaceLifecycle,LimitRanger,ServiceAccount,PersistentVolumeLabel,DefaultStorageClass,DefaultTolerationSeconds,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota \
--kubernetes-version=v1.10.0
```

## 1.4 Enabling shell autocompletion (optional)

```shell
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

## 1.5 Stop Minikube
```shell
rm -rf .minikube
miniube status
```

# 2. Istio Installation  

## 2.1 Download istion
```shell
curl -L https://git.io/getLatestIstio | sh -
```

## 2.2 Add the istioctl client to your PATH
```shell
cd istio-0.7
export PATH=$PWD/bin:$PATH
```

## 2.3 Install Istio's core components.
```shell
kubectl apply -f install/kubernetes/istio.yaml
```

## 2.4 verify your installation
> Note: If your cluster is running in an environment that does not support an external load balancer (e.g., minikube), the EXTERNAL-IP of istio-ingress says <pending>. You must access the application using the service NodePort, or use port-forwarding instead.  

```shell
kubectl get svc -n istio-system
kubectl get pods -n istio-system
```



# Reference
[Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)  
[Install Istio](https://istio.io/docs/setup/kubernetes/quick-start.html)  






