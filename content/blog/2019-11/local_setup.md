---
title: "Local Setup for Learning Kubernetes"
date: 2019-12-09T13:59:48+08:00
draft: false
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
toc: true
---

## Introduction

There are multiple ways of setting up kubernetes. To play around or learn kubernetes
you can set it up via multiple ways such as:
- Minikube
- MicroK8s
- Kubeadm

The [kubeadm][kubeadmlink] tool can also be used for setting up a local viable cluster with 
the help of a config management tools such as Ansible. A detailed step by step tutorial for 
installing kuberetes with ansible on a local cluster can be found [here][kubeansible].

However for running a prodcuction grade cluster in cloud it's better to use a managed
service from providers such as GCP, AWS, Azure or DigitalOcean.

For a local development or learning setup `minikube` is the easiest to start with and we 
will go through steps to setup a local minikube cluster.

A typical kubernetes deployments consists of master and worker node with components 
shown in the image below.

{{% img-no-border %}}<img name="Pods" src="/images/blog/kubernetes/local_deployment/kube_m_w.png"/>{{% /img-no-border %}}

Minikube bundles all these components together in a single node kubernetes cluser. This gives us a simple setup
to work and interact with.

{{% img-no-border %}}<img name="Pods" src="/images/blog/kubernetes/local_deployment/minikube.png"/>{{% /img-no-border %}}

### Installing Kubectl

To interact with a kubernetes cluster we need a utility `kubectl`. The kubectl utility can interact with both local
as well as a remote setup with some configuration. 

Inorder to install kubectl utility on linux
1. Download the latest release with the command
```shell
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
2. Validate the binary
Download checksum file
```shell
curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

Validate the binary
```shell
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```
3. Install kubectl
```shell
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

For more detail visit [here][installkctl]

If you want to enable _shell completion_ for kubectl then please see this [link][completion]
it explains how to add it for `bash`, `fish` and `zsh`.

### Installing Minikube

To install minikube on linux follow the commands:
1. Download the binary
```shell
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

2. Install the binary
```shell
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

3. Setup a local kubernetes cluster
Minikube needs a driver to setup the cluster on the local machine. A list of drivers
supported can be found [here][minkdriver].
I prefer the docker driver as it's easier to setup and clean and is also a very 
common utility for dev and ops people.
To start the cluster with docker driver, run the command:
```shell
minikube start --driver=docker
```

To make this the default driver we can run the following command
```shell
minikube config set driver docker
```

Once you are done with the setup you can start and stop the cluster with the
help of `minikube`.
```shell
minikube start  #starts the cluster
minikube stop   #stops the cluster 
```

You can interact with the cluster using `kubectl` e.g.
```bash
kubectl get nodes
```
{{< highlight go >}}
kubectl get nodes
{{< / highlight >}}

This should return the output as such
```
NAME       STATUS   ROLES                  AGE   VERSION
minikube   Ready    control-plane,master   37h   v1.22.3
```

## Summary 
In the article we took a look at how to setup kubectl utility and minikube for
local development on kubernetes.


[kubeadmlink]: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
[kubeansible]: https://digitalvarys.com/install-kubernetes-cluster-with-kubeadm-and-ansible-ubuntu/
[installkctl]: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
[completion]: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#optional-kubectl-configurations-and-plugins
[minkdriver]: https://minikube.sigs.k8s.io/docs/drivers/