---
layout: index
title: "Hello, Openshift!"
category: kubernetes
date: 2020-10-05 15:17:55
---

# Openshift Memo

## Adding roles to users

https://docs.openshift.com/container-platform/4.5/authentication/using-rbac.html

```
#local role
oc adm policy add-role-to-user <role> <user> -n <project>

#For example, you can add the admin role to the alice user in joe project by running:
oc adm policy add-role-to-user admin alice -n joe

#cluster role
oc adm policy add-cluster-role-to-user cluster-admin <user>
```


## View env in a container

```
podman exec -it todoapi env
```

## OpenShift Terminology

Term | Definition
---|---
Infra Node| A node server containing infrastructure services like monitoring, logging, or external routing.
Console | A web UI provided by the RHOCP cluster that allows developers and administrators to interact with cluster resources.
Project | OpenShift's extension of Kubernetes' namespaces. Allows the definition of user access control (UAC) to resources.




## CMD and ENTRYPOINT

```
ENTRYPOINT ["command", "param1", "param2"]
CMD ["param1","param2"]

```

## Saving and Loading Images

```
sudo podman save [-o FILE_NAME] IMAGE_NAME[:TAG]

sudo podman load -i mysql.tar
```

## Configuring Registries in Podman

```
cat /etc/containers/registries.conf
```

## Build Kubernetes Operators from Helm Charts in 5 steps

https://www.openshift.com/blog/build-kubernetes-operators-from-helm-charts-in-5-steps

## Tutorial for Helm-based Operators

https://sdk.operatorframework.io/docs/building-operators/helm/tutorial/

## Memcached Helm Operator 

https://github.com/operator-framework/operator-sdk-samples/tree/master/helm

## Create a route

```
oc create route passthrough --service $(oc get svc | grep <Service_Name> | awk '{print$1}')
```


## Allowing Pods to reference images across projects

https://docs.openshift.com/container-platform/4.5/openshift_images/managing_images/using-image-pull-secrets.html#images-allow-pods-to-reference-images-across-projects_using-image-pull-secrets

## Managing SCCs in OpenShift

https://www.openshift.com/blog/managing-sccs-in-openshift

## Use podman push image skip tls verify

```
podman push --tls-verify=false <Registry>/<project>/<Image>:<Tag>

```

## ssh to openshit work node

```
ssh core@hostname

#View image registry info
cat /etc/hosts
```

## Exposing the registry

https://docs.openshift.com/container-platform/4.3/registry/securing-exposing-registry.html

```
oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

HOST=$(oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}')
podman login -u $(oc whoami) -p $(oc whoami -t) --tls-verify=false $HOST
```

## Install helm on openshift

### 1. Update Environment Variable

```
vi ~/.bash_profile
export TILLER_NAMESPACE=tiller
source ~/.bash_profile
```

### 2. Create a new project

```
oc new-project tiller
oc project tiller
```

### 3. Init helm

```
wget https://get.helm.sh/helm-v2.14.2-linux-amd64.tar.gz
tar xzvf helm-v2.14.2-linux-amd64.tar.gz
cd linux-amd64
chmod 755 helm
rm -rf /usr/local/bin/helm
mv helm /usr/local/bin/helm
helm init --client-only
```

### 4. Create tiller by openshift template

```
oc process -f https://github.com/openshift/origin/raw/master/examples/helm/tiller-template.yaml -p TILLER_NAMESPACE="tiller" -p HELM_VERSION=v2.14.2 | oc create -f -
oc rollout status deployment tiller
helm version
```

### 5. Use helm in your project

```
oc new-project ida-helm
oc project ida-helm

## bind role
oc adm policy add-role-to-user edit "system:serviceaccount:tiller:tiller"
## get bind role info
oc describe rolebinding.rbac -n ida-helm
```

