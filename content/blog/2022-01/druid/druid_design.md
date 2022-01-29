---
title: "Druid Design Part1"
date: 2022-01-10T08:22:48+08:00
draft: true
tags: ["druid"]
categories: ["Druid"]
series: ["Druid"]
---


Druid has a multi process, distributed architecture.

The different processes are:
`Coordinator`: Manages data availability on the cluster.
`Overlord`: Controls the assignment of data ingestion workloads.
`Broker`: Process quries from external clients
`Router`: They route requests to Brokers, Coordinators and Overlords.
`Historical`: Processes stored queryable data
`MiddleManager`: Processes ingest data

When deploying druid, we can separate all the above process on 3 type of server 
nodes. _Master_, _Query_ and _Data_.

_Master_: Coordinator, Overlord
_Query_: Broker and Router
_Data_: Historical and MiddleManager

One of the main reasons behind the above segregation is allocation of compute and memory resources. Each process has 
different kind of requirement so segregating them helps in better creation and utilisation of resources.

### External Dependencies

#### Deep Storage
Druid uses deep storage to store any data that has been ingested. This storage is accessible by all the druid drivers. 
The distributed object store could be S3 or HDFS. The deep storage acts as a backup of the data.

#### Metadata Storage
Metadata store is used for various shared system metadata such as usage information and task information. 
The metada store is typically an RDBMS such as postgres.

#### Zookeeper
Zookeeper is used for insternal service discovery in a distributed system, co-ordination and leader election.

