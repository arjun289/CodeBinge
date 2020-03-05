
---
title: "Kafka Introduction"
date: 2019-11-09T13:59:48+08:00
draft: true
tags: ["kafka"]
categories: ["Kafka"]
series: ["kafka"]
---

## Publish/Subscribe paradigm

- Softwar systems are becoming more and more complex.
- Days of monoliths are over(as many say). The reason behind this is 
  software is built over existing infrastructure which allows everyone to 
  think big allowing multiple types of processing, metrics analysis, 
  active monitoring are now part of every application out there. 
  This leads to a web of dependencies. 
- When applications start they take a technical debt of creating a mesh
  by making the services talk directly with each other as the services
  are few.
- As the number of services grow companies decide to pay back this debt
  and try to decouple services by taking away the responsibility of 
  sharing and putting it in a single central service which can be queried
  for data of a particular type.
  > Image here describing the above.
- Over-time this also becomes tedious and people have to centralize the entire
  messaging system and compartmentalize the messages into topics. 
  This is what is known Pub/Sub way of handling of messages.
  > Image here describing the above.

## Meet Kafka
Kafka is a publish/subscribe messaging system. It is often referred to as a "distributed commit log". 
> Have to add more info here and link it to a blog which tells more about the internals
of kafka(create a rabbit hole :D)

### Messages and Batches
The unit of data within kafka is a __message__. Message has some metadata associated with it,
which is referred to as a key. Keys are used when messages are to be written reliably to a partition. A consistent hash of the key is created, this hash modulo the number of partitions 
matches it to the same partition always.

Batch is a collection of messages written to the same partition. These reduces network overhead
associated with propagating a message. However, it comes with a tradeoff in terms of time it 
takes to propagate a single message. 

### Schemas
Messages in kafka are opaque, what this means is kafka doesn't care about the content of the
message and it's an array of bytes. Though adding a schema is recommended so that contents
can be understood. Apache Avro is suggested for serialization and desirialization. Schema
creates consistency and decouples the producer and consumer of the message from each other. In 
the absence of a schema, change in data format of a producer would need to be accompanied by a
correspodnding change in the consumer.

### Topics and Partitions
> Add an image here for understanding.

Topics help categorize messages in Kafka. Topics are further broken down into multiple partitions. A partition is a `commit log`, message are written to it in an append only fashion. Partitions also help in providing redundancy, a parition is hosted on multiple
servers. This allows the data to be redundant at the same time the topic can be scaled
horizontally.

### Producers and Consumers
Users of the kafka are kafka clients. There are two basic types producers and consumers.
There are two other advanced types called Kafka Connect API for data integration and Kafka
Streams for stream processing. 
Producers create new messages. By default, the producer doesn't care which partition the message goes to for a topic. In some conditions, it may be required for a producer/publisher
to direct the message to a particular paritition. This can be handled with the help of custom
partitioner wherein business rules can be added to handle, message direction to a particular
partition.



