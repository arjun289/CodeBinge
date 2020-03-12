---
title: "ElasticSearch Installation"
date: 2020-03-10T08:22:48+08:00
draft: false
tags: ["elastic_search"]
categories: ["Elasticsearch"]
series: ["elasticsearch"]
---

## Installation

There are multiple ways to install and use ElasticSearch and Kibana. 

### ElasticCloud
[Elastic Cloud ][deployment_link] is a software as a service
provided by elastic. 
The above allows you to deploy a cluster in the cloud and run your application.
It's the easiest option, however it costs you to run the application in cloud.
There is free trial of 14 days available though.

### Running on Linux, MacOS, Windows
The elasticsearch consists of a set of binaries to run on respective operating
system, it can be downloaded from on [elasticsearch downloads][download] page.

#### MacOS and Linux
Easy steps to download and run Elasticsearch on linux. Please change the 
package version if you want a different version.

```shell
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.6.1-darwin-x86_64.tar.gz      //download the binaries
$ tar -xvzf elasticsearch-7.6.1-darwin-x86_64.tar.gz        // extract the files     
$ cd elasticsearch-7.6.1
$ bin/elasticsearch  // runs the elasticsearch server in forgeground
```

But be warned `elastic search` needs java to be properly set on the machine
and it might lead to some trouble if setup is not proper. To avoid any java 
requirement related issues, we can use the dockerized deployment which is
much easier to setup.
> provide a resolution for the java problem.

> Add steps for windows


### Running with Docker


[deployment_link]: https://www.elastic.co/cloud/elasticsearch-service/signup?elektra=downloads-overview&storm=elasticsearch
[download]: https://www.elastic.co/start
