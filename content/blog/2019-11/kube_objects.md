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

> If you have followed the previous part till end then you can run the following 
commands else follow steps to [create replica 
sets]({{< ref "./kube_basic.md#scaling-replica-sets" >}}).

__First method__

```shell
kubectl logs deploy/my-apache
```

and it spits out the following on the terminal.

```
Found 2 pods, using pod/my-apache-5d589d69c7-mb7wx
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.3. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.10.3. Set the 'ServerName' directive globally to suppress this message
[Thu Nov 21 14:23:27.128189 2019] [mpm_event:notice] [pid 1:tid 139734356759680] AH00489: Apache/2.4.41 (Unix) configured -- resuming normal operations
[Thu Nov 21 14:23:27.150818 2019] [core:notice] [pid 1:tid 139734356759680] AH00094: Command line: 'httpd -D FOREGROUND'
```
