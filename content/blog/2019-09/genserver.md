---
date: 2019-10-09T12:47:47+08:00
draft: false
title: "Elixir GenServers 101, Part 1"
tags: ["elixir", "OTP", "genservers"]
series: ["elixir/otp"]
categories: ["Elixir"]
---

### What are Server Processes

A server process can be defined as one with the following behaviour :

- Long running process.
- It can maintain state.
- It can process messages and react to them.
- It may modify the internal state.
- It may send a reply back to the client.

Server processes are important in building highly concurrent systems.
Erlang provides an important abstraction to handle server processes in it's 
portion  of OTP framework, known as a `Genserver`.

##### Implementing the Server Process

But before we go into nitty gritties of Genserver. Let's build a minimal version
of this abstraction ourselves.
To do this we have to implement a process which has the behaviour mentioned 
above for a server process.

The generic module that we will create will perform tasks common to server 
processes, while specific tasks are left to concrete implementations.

First steps first, create a new project with :
```bash
    mix new server_process
```

Inside your project create a module `ServerProcess.GenericServer` in

`lib/server_process/generic_server.ex`.

This module handles our generic code implmentation, the specifics of 
which(e.g. inital state, processing) would be handled by a concrete 
implementation which  we can handle by using the concrete modules as callbacks 
(will become more clear as we move forward). 

```elixir
defmodule Server.GenericServer do
	def start(callback_module) do
		spawn(fn ->
			initial_state = callback_module.init()
			loop(callback_module, initial_state)
		end)
	end
  ...
end
```

`GenericServer.start(callback_module)` takes a module name, and spawns a process 
with the initial state, being set by a function `init()` exposed in the 
`callback_module`. In order to enforce this rule of exposing the `init` function
we can use [`behaviours`][behaviour].

```elixir
defmodule Server.GenericServer do
	@callback init()
...
end
```
With this change all the callback modules implmenting the `GenericServer` behaviour 
will have to implement the _callbacks_ added.

Next step would be to add the `loop(callback, state)` function. The loop function 
makes use of a [tail recursion][tail_recursion] call to maintain the state and 
keep the process running forever using infinite recursion.

```elixir
defmodule Server.GenericServer do
	@callback handle_call(request :: tuple, state :: any) ::
    	{response :: any, new_state :: any}
  	@callback handle_cast(request :: tuple, state :: any)
    	:: {new_state :: any}
	....

	def loop(callback_module, state) do
    new_state =
      receive do
        {:call, from, request} ->
          {result, state} = callback_module.handle_call(request, state)
          send(from, {:result, result})
          state
		{:cast, _from, request} ->
			callback_module.handle_cast(request)
      end

    loop(callback_module, new_state)
  end
  .....
end
```
The `loop/2` function handles the generic logic of sending and receiving messages,
the specific logic of what to do with the request is handled by the callback module's
`handle_call/2` and `handle_cast` functions. This brings us to the next set of 
callbacks that should be implemented.

A request can be synchronous or asynchronous depending on the requirement. To 
differentiate between them we use `:call`(synchronous) and `cast`(asynchronous) 
going with the convention in Elixir. In a synchronous request the calling process
waits for a response from the server process.


[behaviour]: https://elixir-lang.org/getting-started/typespecs-and-behaviours.html
[tail_recursion]: https://blog.appsignal.com/2019/03/19/elixir-alchemy-recursion.html
