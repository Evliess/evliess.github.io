---
layout: index
title: "Basic of Kubernetes"
category: kubernetes
date: 2018-07-09 15:17:55
---

# Kubernetes Memo

## Kubernetes Terminology

Term | Definition
--- | ---
Node | A server that hosts applications in a Kubernetes cluster.
Master Node | A node server that manages the control plane in a Kubernetes cluster. Master nodes provide basic cluster services such as APIs or controllers.
Worker Node | Also named Compute Node, worker nodes execute workloads for the cluster. Application pods are scheduled onto worker nodes.
Resource| Resources are any kind of component definition managed by Kubernetes. Resources contain the configuration of the managed component (for example, the role assigned to a node), and the current state of the component (for example, if the node is available).
Controller | A controller is a Kubernetes process that watches resources and makes changes attempting to move the current state towards the desired state.
Label | A key-value pair that can be assigned to any Kubernetes resource. Selectors use labels to filter eligible resources for scheduling and other operations.
Namespace | A scope for Kubernetes resources and processes, so that resources with the same name can be used in different boundaries.

## Switching

![Switching](./images/kubernetes/switching.png)

## Routing

![Routing](./images/kubernetes/routing.png)

## Gateway
![Gateway](./images/kubernetes/gateway.png)
![DefaultGateway](./images/kubernetes/default_gateway.png)

## Namespace
![ns_1](./images/kubernetes/namespace_1.png)
![ns_2](./images/kubernetes/namespace_2.png)
![ns_3](./images/kubernetes/namespace_3.png)
![ns_4](./images/kubernetes/namespace_4.png)
![ns_5](./images/kubernetes/namespace_5.png)

## Using Secrets as environment variables

https://kubernetes.io/docs/concepts/configuration/secret/

## init-containers

https://kubernetes.io/docs/concepts/workloads/pods/init-containers/#examples







