---
date: 2019-10-08T12:47:47+08:00
draft: true
title: "Elixir GenServers 101, Part 2"
tags: ["elixir", "OTP", "genservers"]
series: ["elixir/otp"]
categories: ["Elixir"]
---

### What are Genservers
Inorder to support concurrency, Erlang/OTP has defined different generic behaviours.
These behaviours abstract away the generic code while allowing the developer to 
plugin to them using callbacks. A _GenServer_ behaviour defines the generic
code for a __stateful server process__. In part1 of the blog on genservers we 
discussed the properties of a long running stateful server process.

