---
title: "Kubernetes Deployment Strategies"
date: 2019-11-09T13:59:48+08:00
draft: false
tags: ["kubernetes"]
categories: ["Kubernetes"]
series: ["kubernetes"]
toc: true
---

Deployment strategies are methods use to deploy applications on Kubernetes.

## Recreate Strategy

## Rolling Update

## Blue Green Strategy
New deployment is made across the old deployment. Then the traffic is moved
from old to new in one go.

deployment and service with label v1
create deployment with label v2 on kubernetes
change label in service to v2

## Canary

In canary deployment a small percentage of requests are routed to the new deployment.


### Comparison of different Strategies