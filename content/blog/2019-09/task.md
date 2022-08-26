---
title: "Elixir Tasks"
date: 2019-10-07T13:59:48+08:00
draft: false
tags: ["elixir", "OTP", "genservers"]
categories: ["Elixir"]
series: ["elixir/otp"]
---

## Overview

Tasks are an abstraction build over BEAM processes which allow easy management
of the spawned process instead of directly handling the nitty gritties.
Elixir/Erlang provides `spwan` and `recieve` functions for spawning and handling
communication with process however they are cumbersome to deal with in a complex
setting and may require us to write code for error handling, receiving requests
and managing the entire lifecycle of the process. The Task module provides a simple 
API to interact with the processes. 

In this blog we will try to understand how to use the Task module functions. How
to start tasks, retrieve results, process large amount of data using the Task 
module. I have been working in the IoT domain and will be showing examples from the same.
IoT is quite data intensive condierring use caess where data packets can be range from 
generation every millisecond to a single data point per day.

A detailed list of functions exposed by the `Task` and `Task.Supervisor` module can be found 
[here](https://hexdocs.pm/elixir/1.12/Task.Supervisor.html#async/3)
However,  the set of functins we will take a look at in this blog are the following:
```

```

## Creating the Project
Let's give a cool name to our project we can call it SenseEx. The project
will be receiving IoT data and processing it.
To create the application 