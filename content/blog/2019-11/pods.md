---
title: "Kubernetes: PODs"
date: 2019-11-09T13:59:48+08:00
draft: false
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
toc: true
---

## PODS

Kubernetes's ultimate aim is to deploy an application in the form of containers
on machines. Kubernetes doesn't deploy containers directly, it encapsulates
it into an object known as a `pod`. 
As the traffic increases to a cluster these `pods` are scaled to meet the demand.
A `pod` can contain multiple containers of different types. Sometimes helper containers
are deployed on the same pod however such instances are very rare. It's a good
practice to have only one container per pod as it keeps the architecture simple.


{{% img-no-border %}}<img name="Pods" src="/images/blog/kubernetes/pods/pods1.png"/>{{% /img-no-border %}}


### Containers and their use
A genuine question comes to mind whenever we start with Kubernetes is why we need pods 
if there are containers. Containers are used for deploying a single application(process) in 
an isolated way. The main idea behind this philosohpy is that it becomes easier to manage 
logs, and if there is an exception then the orchestrator can restart the process.
We will have hard time figuring out the issues if there are multiple processes running in a single
container. Also, there might be a need that some of the services should keep running in case another
fails, when a container goes down it brings all the processes to halt. 

### Organize containers inside PODS 
Since containers are ideal for running a single process it makes sense to have another layer 
above it to encapsulate multiple coupled processes in a single construct, as they might be communicating
via IPC or local file systems and benefit from running on a single machine. The containers running 
within a POD get the same environmnt as if they are running on the same host. 
The containers running inside the POD share the same IP address and port space. All the containers
also have the same loopback interface which means they can reach each other over the localhost.

#TODO: write how to group containers 


### Create a POD
The simplest way to create a pod is by running the command
```shell
kubectl run nginx --image nginx
```

This will create an nginx pod with an nginx image, the image is pulled from docker hub, kubernetes 
can also be configured to download the image from a private repo. Now if you run the command
`kubectl get pods`. The command returns all the pods in the cluster. 
It will return the following output
```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          18s
```
In order to access more info we can give the wide option as such.
```
kubectl get pods -o wide
```
It returns more detail about the pods.

```
NAME    READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          5m39s   172.17.0.3   minikube   <none>           <none>
```

### POD Description

A very important command for pods is `kubectl describe pod <pod_name>` command, it returns a description 
of the pod. It returns various information about the pod such as _name_, _name_space_, _status_ etc.
The describe command also returns all the events associated with the pod.

```
Name:         nginx
Namespace:    default
Priority:     0
Node:         minikube/192.168.49.2
Start Time:   Thu, 26 May 2022 21:55:34 +0530
Labels:       run=nginx
Annotations:  <none>
Status:       Running
IP:           172.17.0.3
IPs:
  IP:  172.17.0.3
Containers:
  nginx:
    Container ID:   docker://25e83f798f901fa6ff98ecdf3126a8347c7b260f7abaf8aee636e65cde6dfcb3
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2d17cc4981bf1e22a87ef3b3dd20fbb72c3868738e3f307662eb40e2630d4320
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 26 May 2022 21:55:39 +0530
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-5ssgm (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-5ssgm:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  22m   default-scheduler  Successfully assigned default/nginx to minikube
  Normal  Pulling    22m   kubelet            Pulling image "nginx"
  Normal  Pulled     22m   kubelet            Successfully pulled image "nginx" in 4.526431547s
  Normal  Created    22m   kubelet            Created container nginx
  Normal  Started    22m   kubelet            Started container nginx
```

### Creating PODs with YAML
Kubernetes uses yaml files for creating different objects. A `pod` can also be created by using
a YAML definition file. A kubernetes config file for a pod has the following structure
```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels: 
    app: server
spec:
  containers:
    - name: nginx-container
    - image: nginx
```

`apiVersion`: What api version we are using for creating the object
`kind`: The kind of object
`metadata`: info about the pod such as name, labels etc. The name and labels are 
  important. The name key is a string, the label is a dictionary where we can 
  have any key value pairs which can be used for grouping pods with particular tags
  together.
`spec`: contains info to setup the object for pods it can be containers

To create a pod from the yml definition file we use the command
```shell
kubectl create -f <file_name>
##or
kubectl apply -f <file_name>
```


While you can create a yaml and remember all these keys, however a trick is to 
generate the file with dry run command. 
```shell
kubectl run --dry-run=client nginx --image nginx -o yaml > pod.yml
```

This will generate a file with the above content which you can edit as per your
requirement and then use it for creating a pod.
