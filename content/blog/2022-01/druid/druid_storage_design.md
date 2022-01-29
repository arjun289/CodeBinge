---
title: "Druid Design Part2"
date: 2022-01-10T08:22:48+08:00
draft: true
tags: ["druid"]
categories: ["Druid"]
series: ["Druid"]
---

The part 2 of the druid design article focuses on how the data is stored in apache druid. 

Data in druid is stored in datasources which is similar to a table in RDBMS. Each datasource is partitioned by time
and can then be further partitioned by other attributes. Each time range is called a _chunk_. Within a chunk 
data is partitioned into one or more segments.

A segment is a single file containing millions of rows of data. A data source can have segements ranging from a
few hundred to a few millions. The segements are created by the _middlemanager_. Segements are mutable and uncommitted 
inititally however periodically they are committed and published to to deep storage, after which they become immutable.

## Indexing
