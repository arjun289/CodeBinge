---
title: "Kubernetes: ReplicationController and Replicasets"
date: 2019-11-09T13:59:48+08:00
draft: true
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
toc: true
---


## Replication Controller


## ReplicaSet
The ReplicaSet is process for monitoring pods. It makes sure that the required
number of replicas for a pod are running. If pods lesser than the replicas are running
then it makes sure to start them, in scenarios where more pods are started it automatically
terminates them to maintain the replicas specified.


#### Create
```
kubectl create -f replicaset-defn.yml
```
####  Get
```
kubectl get replicaset
```

####  Delete
```
kubectl delete replicaset <rs-name>
```

#### Edit
```
kubectl edit rs <rs-name>
```
When we run this command kubernetes opens the temporary file it creates for the
RS, this is not the same as the config file that we created and used for building 
the replicaset.

### Scale
1. Modify the file change number of `replicase` and use replace command
```
kubectl replace -f replicaset-defn.yml
```
2. Use the scale command either with config file or the name of replicaset
```
kubectl scale --replicas=6 -f replicaset-defn.yml
## or
kubectl scale --replicas=6 -f replicaset <replicaset-name>
```

3. Edit the replicaset file
```
kubectl edit rs <rs-name>
```
This opens the temporary file we created for replicaset, changes made to this 
file are then automatically executed by kubernetes.

## ReplicaSet vs ReplicationController
The only difference b/w `ReplicaSet` and `ReplicationController` is in terms of
the __selectors__ field. 
In ReplicationController the `selector` is an optional field which is used for matching
the labels of the pods deployed by it. It automatically infers it from the lables
specified in the template. In case of replicationcontroller the selector field is quite
limited in terms of features as it only provides equality based selectors, which are =, ==, !=. 

Incase of ReplicaSet `selectors` is a mandatory field. It provides support for both
equality as well as Set-based  label selectors. Three types of set-based selectors
are `in, notin, exists`.
