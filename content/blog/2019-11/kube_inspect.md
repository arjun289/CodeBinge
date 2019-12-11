---
title: "Inspecting Kubernetes Objects"
date: 2019-11-10T13:59:48+08:00
draft: false
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
---

During a deployment the orchestrator performs some operations on the 
containers.
To see the operations, we can make use of some commands which paints a 
clear picture on series of steps taken to reach the goal.
The two main commands are __logs__ and __describe__. 

> If you have followed the previous part till end then you can run the following 
commands else follow steps to [create replica 
sets]({{< ref "./kube_basic.md#scaling-replica-sets" >}}).

__Log__
If we use the log command to inspect the deployment we created `my-apache`,

```shell
kubectl logs deploy/my-apache
```

it spits out the following on the terminal.

```
Found 2 pods, using pod/my-apache-5d589d69c7-mb7wx
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.3. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.3. Set the 'ServerName' directive globally to suppress this message
[Thu Nov 21 14:23:27.128189 2019] [mpm_event:notice] [pid 1:tid 139734356759680] AH00489: Apache/2.4.41 (Unix) configured -- resuming normal operations
[Thu Nov 21 14:23:27.150818 2019] [core:notice] [pid 1:tid 139734356759680] AH00094: Command line: 'httpd -D FOREGROUND'
```

Inorder to inspect the pods based on some label, we can use the command

```shell
kubectl logs -l run=my-apache
```

here `-l` is for label and when we created the replica sets for my-apache, by default
a label is associated with the pods with the name of the cluster.

```
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.6. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.6. Set the 'ServerName' directive globally to suppress this message
[Sun Nov 24 23:15:59.252042 2019] [mpm_event:notice] [pid 1:tid 140337709847680] AH00489: Apache/2.4.41 (Unix) configured -- resuming normal operations
[Sun Nov 24 23:15:59.252156 2019] [core:notice] [pid 1:tid 140337709847680] AH00094: Command line: 'httpd -D FOREGROUND'
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.5. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.5. Set the 'ServerName' directive globally to suppress this message
[Sun Nov 24 23:15:08.958846 2019] [mpm_event:notice] [pid 1:tid 140277681194112] AH00489: Apache/2.4.41 (Unix) configured -- resuming normal operations
[Sun Nov 24 23:15:08.959020 2019] [core:notice] [pid 1:tid 140277681194112] AH00094: Command line: 'httpd -D FOREGROUND'
```

What's to note here is logging using kubernetes built in commands is fine for
few pods. However we should consider using a thrid party system for better
logging as using the built-in commands is taxing after a few pods. This is infact
true for a number of container orchestration tools present out there.

__describe__

The second way to inspect objects is with the _describe_ command.

As is evident from the name itself, the describe command gives a very detailed 
inspection. 

To inspect a pod we can use the command

```shell
kubectl describe pod/<pod-name>
```

we can find the names of the pods running with command
```shell
kubectl get pods
```

This gives a very detailed description about the pod, containers present, conditions
and events.

------------------------------------------

One nice feature is to use the `watch` feature.
This one gives us a running log for all the operations being performed.
To use this you can use the command

```shell
kubectl get pods -w
```

```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-5d589d69c7-4p8qd   1/1     Running   0          46m
my-apache-5d589d69c7-jz6nc   1/1     Running   0          47m
```

It will return the following and let's say if you add a new replica or delete an
existing pod, then you can see the running log.
Try deleting one of the pods with the command

```shell
kubectl delete pod/my-apache-5d589d69c7-4p8qd
```

and you can see the changes happening.
