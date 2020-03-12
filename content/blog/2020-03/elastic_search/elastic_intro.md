---
title: "ElasticSearch Introduction"
date: 2020-03-10T08:23:48+08:00
draft: false
tags: ["elastic_search"]
categories: ["Elasticsearch"]
series: ["elasticsearch"]
---

## What is elastic Search
> Add data
Built with Java, based on Apache lucene.
Easy to use, and highly scalable.

## Use Cases
> Add data

## Overview of ElasticStack
ElasticStack consists of a few different products:
- X-Pack: Adds features to ElasticSearch and Kibana, 
    - Security: Authentication and authroization.
    - Monitoring: Gain insights into how ELK stack is working.
    - Alerts:  For CPU usage, user behaviour, etc.
    - Reporting:
    - Machine Learning: Enables Kibana to use machine learning.
    - Graph: Relationships 
- Kibana: Analytics and visualization engine. Anomaly detection and machine 
learning. 
- Beats: 
- Logstash: Data processing pipeline. 
- ElasticSearch


## Storing Data in Elastic Search
> Add data

### Document
A document in elastic search corresponds to a row in database. 
{
    "name": "iron man",
    "work_place: "stark industries",
    "team": "avengers"
}

Each field in the document corresponds to a column in a table.