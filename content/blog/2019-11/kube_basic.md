---
title: "Kubernetes Basics"
date: 2019-11-09T13:59:48+08:00
draft: false
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
---

- Kubernetes: The whole orchestration system for containers. Also called K8s.
- Kubectl: CLI to configure kubernetes and manage apps. Pronounces Kube-cuttle
           or kube-c-t-l.
- Node: A single server in kubernetes cluseter.
- Kubelet: Kubernetes agent running on nodes.
- Control Plane(Managers): Set of containers managing the cluster. Runs
  APIs, DNS, Database, scheduler, etcd, controller manager etc.


### Control Plane(Master)

etcd -> A distributed key value storage system which uses Raft consensus 
algorithm to manage a highly-available replicated log.

api -> A set of calls to talk to the kubernetes engine running on master nodes.

Scheduler -> Controls how or where containers are placed on nodes. 
 
Controller Manager -> Looks at state of whole cluster using APIs. Receives
orders from user and makes the system work accordingly.

Core DNS -> Manage networking.

### Nodes

kubelet ->

kube-proxy -> 
