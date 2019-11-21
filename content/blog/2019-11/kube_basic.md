---
title: "Kubernetes Introduction"
date: 2019-11-09T13:59:48+08:00
draft: false
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
---

## Basic Terms
- __Kubernetes__: The whole orchestration system for containers. Also called K8s.
- __Kubectl__: CLI to configure kubernetes and manage apps. Pronounces Kube-cuttle
           or kube-c-t-l.
- __Node__: A single server in kubernetes cluseter.
- __Kubelet__: Kubernetes agent running on nodes.
- __Control Plane(Managers)__: Set of containers managing the cluster. Runs
  APIs, DNS, Database, scheduler, etcd, controller manager etc.

{{% img-no-border %}}<img name="Code" src="/images/blog/kubernetes/introduction/kube_basic.png" width='600px'/>{{% /img-no-border %}}

### Control Plane(Master)

`etcd` -> A distributed key value storage system which uses Raft consensus 
algorithm to manage a highly-available replicated log.

`API` -> A set of calls to talk to the kubernetes engine running on master nodes.

`Scheduler` -> Controls how or where containers are placed on nodes. 

`Controller Manager` -> Looks at state of whole cluster using APIs. Receives
orders from user and makes the system work accordingly.

`Core DNS` -> Manage networking.

### Nodes(Workers)

kubelet ->

kube-proxy -> 

### Installing locally

To use kubernetes locally we can use [MicroK8s][mik8]. There are other ways 
to install, but I find this one, the most convenient.

### Kubernetes Container Abstractions
- Pod: One or more nodes running together on one Node.
       - Basic unit of deployment. Containers are always in pod.
- Controller: For creating/updating pods and other objects.
    - Many types of controllers Deployment, ReplicaSet, StatefulSet,
      DaemonSet, Job, CronJob etc.
- Service: network endpoint to connect to a pod.
- Namespace: Filtered group of objects in cluster. A way to filter view.

### Run, create, apply

Running commands in kubernetes are unopinionated which means, there are
multiple ways to achieve the same task.

```
kubectl run(changing to be only for pod creation, closest to docker run)
kubectl create(create some resources via CLI/YAML)
kubectl apply(create/update anything via YAML)
```

{{% img-no-border %}}<img name="Pods" src="/images/blog/kubernetes/introduction/pods.svg" width='300px'/>{{% /img-no-border %}}

### Verifying installation

After correct installation you can run 

```shell
kubectl version
```

and it should spit out something similar to following 
(if everything is installed correctly).

```
Client Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.3", GitCommit:"b3cbbae08ec52a7fc73d334838e18d17e8512749", GitTreeState:"clean", BuildDate:"2019-11-13T11:23:11Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.3", GitCommit:"b3cbbae08ec52a7fc73d334838e18d17e8512749", GitTreeState:"clean", BuildDate:"2019-11-13T11:13:49Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

### Running pods
Pods can be run either from CLI or YAML.

__CLI example__

Run a pod of nginx server

```shell
kubectl run my-nginx --image nginx
```
To see the result of this operation
run

```shell
kubectl get pods
```

The output of above would be something like this
```
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-669bb4594c-z7g2s   1/1     Running   0          95s
```

### Cleanup
```shell
kubectl delete deployment my-nginx
```

### Scaling Replica Sets

Starting a deployment of one replica/pod

```shell
kubectl run my-apache --image httpd
```

Scaling the replica 

```shell
kubectl scale deploy/my-apache --replicas 2
```

or

```shell
kubectl scale deployment my-apache --replicas 2
```

{{% img-no-border %}}<img name="Replica" 
  src="/images/blog/kubernetes/introduction/replica.svg" 
  width='300px'/>
{{% /img-no-border %}}

There are various ways of achieving the same task from the CLI.

[mik8]: https://microk8s.io/#get-started
