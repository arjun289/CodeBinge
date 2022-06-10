---
title: "Big ideas behind MADS, one stop no-code platform for IIoT"
date: 2022-02-09T13:59:48+08:00
draft: false
tags: ["mads"]
categories: ["MADS"]
series: ["mads"]
toc: true
---

Fast, flexible, and user-friendly, MADS is the leading platform for building IIOT
solutions.


## What is MADS?
MADS is a 100% self-service no-code intuitive IoT platform for rapid implementation of 
enterprise IoT projects. It enables quick development, management, and scaling of IoT 
use-cases.
Any enterprise IoT project has some desired outcome to realize the the ROI of the project.
The desired outcome is what the stakeholders are interested in, however to realize that outcome
there are certain foundational steps:
- Create a design of the IoT project
- Connect and collect all the IoT data
- Define data pipelines and apply the business logic
- Analyze and visualize the generated information from raw data.
- Take action to complete the feedback loop.

The above steps are fundamental to deployment of any IoT project. Conventionally 
these steps were developed as applications and tools by software developers in collaboration 
with domain experts who understood the usecase. However, since these steps are common
an abstraction layer can be created to stop reinventing the wheel and create a horizontal
IoT platform which comes with such features baked in. This kind of support allows
the domain experts to use the platform to build the usecase on their own and rapidly
customize and deploy it for their users.

MADS platform took a novel approach of managing the details of the above mentioned 
steps using the concept of apps. MADS apps can securely combine data visualization with advanced 
analytics to provide easy-to-understand insights and actionable maintenance recommendations. 
It can be used  to manage building and asset health, to enable you to make informed 
reliability decisions and optimize your operations. MADS collects, stores, and transports
data using secure cryptography, resilient even to quantum attacks.

The below tree explains different types of apps in the MADS platform.
{{% img-no-border %}}<img name="Pods" src="https://i.imgur.com/uoXVsYP.png" width='800px'/>{{% /img-no-border %}}

## Architecture
MADS has been designed to be:

`Fault tolerant`: The system is designed to keep running when some unforeseen bugs 
creep in, network connection drops, or the machine running the system crashes. 
In case of such an event, the system falls back to redundant servers.

`Scalable`: The platform is capable of ingesting millons of data points to crate 
powerful analytics. With horizontal scalability baked in it's able to adjust to 
incoming traffic and scales.

`Distributed`: The platform is built as a loose coupling of multiple microservices
working together to support differrent use cases.

`Responsive`: The platform is cotinoulsy updated to provide a fast response time 
for users with p99 < 100ms using autoscaling based on advanced algorithms to 
predict user traffic and self-adjust.

`Secure`: It uses latest cryptographic constructs to manage credentials. Data 
encryption is performed rest and motion for all transports with TLS1.2 and hybrid 
classical & post-quantum encryption

The below diagram shows the key components of the MADS platform

{{% img-no-border %}}<img name="Pods" src="/images/blog/mads/mads-architecture.png" width='1000px'/>{{% /img-no-border %}}

### MADS Transport Services
Transport microservices deals with providing a mechanism for IoT gateways to send data to the cloud.

The supported transports in MADS are:

`HTTP`: HTTP(S) is a general-purpose network protocol that can be used in IoT applications 
for exchanging data. Details can be checked here. The HTTP service is written in Elixir 
lang with support of Phoenix library and secured with TLS.

`MQTT`: MQTT is a light-weight publish-subscribe protocol making it ideal for IoT 
gateways. The MQTT service is written in Elixir lang and the broker used for MQTT is 
Vernemq. The base security layer is over TLS.

`CoAP`: CoAP is a light-weight IoT protocol for constrained devices. CoAP protocol 
is UDP based, but similar to HTTP, it uses a request-response model. CoAP observes
option allows to subscribe to resources and receive notifications on resource change. 
The CoAP service is written in Elixir lang.

`LoRaWAN`: LoRaWAN is a media access control (MAC) protocol for wide area networks. 
It is designed to allow low-powered devices to communicate with Internet-connected 
applications over long-range wireless connections. LoRaWAN can be mapped to the 
second and third layer of the OSI model. It is implemented on top of LoRa or FSK 
modulation in industrial, scientific and medical (ISM) radio bands.

The underlying stack used for creating the microservices HTTP, MQTT and CoAP is Elixir/OTP. 
The LoraWAN service is based on a service from chirpstack to manage LoRa devices, 
Chirpstack provides a network and application server which are stable and battle-tested.

### MADS Microservices
MADS services cater to different business requirements in the domain of IoT. The 
services are modeled around business domains to provide a list of features, this 
is also referred to as a bounded context in DDD, domain-driven design. 

MADS takes a hybrid approach to map apps to microservices. Certain apps which 
have similar requirement in terms of infrastructure(hardware), domain and dependencies
are grouped together in a single microservice.

At present there are 3 different microservices:

#### MADS Core Services
MADS core services are responsbile for administration, multi-tenancy and visualisatons.
Apps such as Role Manager, Entity Manager, Dashboards, Reports, Digital Twin
are part of this service. The service uses a mix of Elixir and Nodejs stacks for
building these domains.

#### MADS Real-time Services
MADS Real-time services are responsible for dealing with huge amount of IoT
data being pushed from various sources. Apps such as IoT Manager, Streams, and Alerts
and Notifications are grouped under this category. This service manages all the
devices as well as services connecting to MADS and pushing data. All the device
related alerts are also managed via these services. Control signals which emerge
from MADS are also pushed to the device via these services.

#### MADS Analytics Services
MADS Analytics services take care of all the statistical as well as Machine learning
based analysis. Apps such as Data Cruncher, Data Insights and AI studio are grouped
under these services. The underlying layers involve big data and batch processing
tools such as spark, airflow to orchestrate and run jobs and generate information
which is placed in the data warehouse to be read by visualisation and reporting 
apps. The stack utilized is a mix of python and elixir to achieve the required
outcome.

### Data Storage
IoT work loads are very data heavy, once your IoT challenges are over your big 
data challenge starts in the IoT world. Since there is no single database which
can cater to all these challenges MADS uses a hybrid data storage model for 
different type of workloads.

#### OLTP
Most of the administration related work in MADS as well as complex relations
among different entities are maintained in an OLTP database, this is also the 
storage layer where all the interactions with users happens the most. For this 
layer we use Postgres as the database. Using an SQL database allows us to maintain
strong transcational guarantees which are essential for OLTP workloads we get on 
MADS.

#### TimeSeries Database
There is a tremendous amount of data being generated by sensors in the IoT world, 
\> 90% of this data is timeseries data coming from various assets being monitored. In
order to deal with this huge volume in real time, we required a database which 
can ingest timeseries data in real time, is distributed so we can scale as the volume 
increases, and can perfrom basic aggregations for real time visualisations. To meet
all these requirements we chose CrateDB. 

#### OLAP Database
Analytics is bread and butter of any IoT solution, it's the final outcome which
the stakeholders depend on for the decision making process. The very need for collecting
a huge amount of data from the sensors is to apply business rules store the data 
and then perform queries against it to draw valuable insights from it. In the 
IoT world the demand for analytics is also on the sub-second level which makes
it quite challenging. In order to meet these requirements we use Apache Druid
a highly optimized database for meeting subsecond ad-hoc queries with high
concurrency requirement.

### Deployment
MADS services are deployed as containerized applications. Containers are the best 
way to bundle and deploy applications. They provide a platform and premise independent 
way of doing deployments. Also, using containers allows for easy continuous integration 
and continuous deployments (CI/CD).

The lightweight nature of containers also allows easy managing which will ensure 
that there is minimum to zero downtime. The containers are managed using a container 
orchestration servcie. 

The MADS platform leverages Kubernetes as an enterprise-grade orchestrator platform 
for all the solutions. It lets you abstract from managing containers lifecycle, mitigation
of node failures, networking, and much more, keeping the focus on the business domain only.
