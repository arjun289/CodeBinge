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
portion  of OTP framework, known as a `Genserver`. It's the essential backbone
over which much of the parallel processing in Elixir is built. 

------------------------------

##### Implementing the Server Process

Inorder to have a better understanding of the `Genserver` behaviour. Let's build 
a minimal version of this abstraction ourselves using the very basic construct 
of OTP, `processes`. To do this, we have to implement a process which has all 
the characteristics mentioned above, of a long running server process.

__What we want to achieve at the end__:
```
1. Define a behaviour(for a long running process with state) which can be extended 
  using callback modules.
2. The behaviour should create a process and initialize it by calling the 
   respective callback module.
3. The behaviour should have a mechanism to keep the process runnning until a signal
   to stop or kill the process is given.
4. The behaviour should expose interface functions to deal with the server process.
5. The interface functions should include functions to make synchronous and 
   asynchronous calls to the process.
```

First steps first, create a new project with :
```bash
    mix new server_process
```

Inside your project create a module `ServerProcess.GenericServer` in

`lib/server_process/generic_server.ex`.

This module will handle our generic code(start process, maintain state etc.) implmentation, 
the specifics of which(e.g. initalizing state, processing, etc.) would be handled 
by a concrete implementation. These implementations can be done using `callbacks`.
(will become more clear as we move forward). 

```elixir
defmodule ServerProcess.GenericServer do
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
  @callback init() :: any
  @callback handle_call(request :: tuple(), state :: any) ::
    	{response :: any, new_state :: any}
  @callback handle_cast(request :: tuple(), state :: any) :: {new_state :: any}
  @callback terminate() :: any}
	....

	def loop(callback_module, state) do
    new_state =
      receive do  #waits for a message to come in the mail box
        {:call, from, request} ->
          {result, state} = callback_module.handle_call(request, state)
          send(from, {:result, result})
          state
		    {:cast, _from, request} ->
			    callback_module.handle_cast(request)
        {:stop, _from, reason} ->
          callback_module.terminate()
          Process.exit(self(), reason)
        end

    loop(callback_module, new_state)
  end
  .....
end
```

The `loop/2` function handles the generic logic of sending and receiving messages,
the specific logic of what to do with the request is handled by the callback module's
`handle_call/2` and `handle_cast` functions. This brings us to the next set of 
callbacks that should be implemented. The last callback `terminate` allows
us to perform any cleanups before terminating our genserver with `Process.exit/2`.

> Whenever we are using tail recursion inside loop function with a `receive` block,
> the process waits for any message to come in its mailbox and executes it when 
> it receives one. One important thing to note here is that when a process is 
> waiting for a message at `receive`, it is put in a suspended state. This 
> prevents wastage of any CPU cycles for the process.

A request can be synchronous or asynchronous depending on the requirement. To 
differentiate between them we use `:call`(synchronous) and `:cast`(asynchronous) 
going with the convention in Erlang. In a `synchronous` request the calling process
waits for a response from the server process. This blocks the client for the 
duration in which processing happens. In an `asynchronous` the client doesn't 
expect a return value so it just sends the request and continues with its work.
The requests are pattern matched to continue with further execution, all the requests
coming in must be tagged with `:cast` or `:call`.

Now that our long running process with state is started, let's move on to 
writing the interface functions which will help us in interacting with this process.
The functions we would implement are:
- `cast`: makes an aysnchromous call, starts the processing and returns.
- `call`: makes a synchronous call and returns with the value after processing.
- `stop`: stops the server process with provided reason.

```elixir
defmodule ServerProcess.GenericServer do
  ....

  def cast(pid, request_params) do
    send(pid, {:cast, self(), request_params})
  end

  def call(pid, request_params) do
    send(pid, {:call, self(), request_params})

    receive do
      {:result, result} ->
        IO.puts(result)
    end
  end

  def stop(pid, reason) do
    send(pid, {:stop, self(), reason})
  end

  ....
end
```

The `cast/2` function takes the pid of the `server process` and the request params
that would be sent to the `callback_module` for processing. Internally it calls
the `Kernel.send/2` with the `pid` and `{:cast, self(), request_params}`. This
will send a message to the mailbox of our server process. On receiving a message
the process will pattern match it, in our case it is `{:cast, _from, request}`
inside the receive block, which will further call the `handle_cast` callback 
in the `callback_module` we initiated our process with.

The `call/2` function similarly takes the pid and the request params and sends
a message to the process mailbox using `Kernel.send/2`. However, since we are
expecting a result we make use of a `receive` block. Processes in elixir interact
with each other using the process mailbox. As can be seen from the loop block that
the server process sends a message back to the calling process. The receive block
prints any message received for our client process.

The `stop/2` function takes the `pid` and `reason`(:normal, :kill). The function
sends a message to the our server process with a reason for termination. On 
receiving the message the server_process makes a call to terminate callback in 
the callback module to perform cleanups and then terminates itself.

{{% img-no-border %}}<img name="my_behaviour" src="/images/blog/elixir/genserver/my_behavior.svg" width=600/>{{% /img-no-border %}}


This is our server process module in it's entirety.

```elixir
defmodule ServerProcess.GenericServer do

  ############### interface functions ############
  # The following functions are run in the client context
    def start(callback_module) do
    spawn(
      fn ->
        initial_state = callback_module.init()
        loop(callback_module, initial_state)
      end
    )
  end

  def cast(pid, request_params) do
    send(pid, {:cast, self(), request_params})
  end

  def call(pid, request_params) do
    send(pid, {:call, self(), request_params})

    receive do
      {:result, result} ->
        IO.puts(result)
    end
  end

  def stop(pid, reason) do
    send(pid, {:stop, self(), reason})
  end

  ################ callbacks for the behaviour ######
  @callback init() :: any
  @callback handle_call(request :: tuple(), state :: any) ::
    	{response :: any, new_state :: any}
  @callback handle_cast(request :: tuple(), state :: any) :: {new_state :: any}
  @callback terminate() :: any

  ################ server calls #################
  # The following functions are run in the server process context

  def loop(callback_module, state) do
    new_state =
      receive do
        {:call, from, request} ->
          {result, state} = callback_module.handle_call(request, state)
          send(from, {:result, result})
          state
		    {:cast, _from, request} ->
          callback_module.handle_cast(request, state)
        {:stop, _from, reason} ->
          callback_module.terminate()
          Process.exit(self(), reason)
      end

    loop(callback_module, new_state)
  end

end

```

----------------------

### Creating a Callback Module

Let's implement a callback module to test out our generic server.
We will create a simple key value store and implement the callbacks defined 
by the behaviour.

```elixir
defmodule ServerProcess.ExampleModule do
  @moduledoc """
  Simple key value store that extends generic server behaviour.
  """

  @behaviour ServerProcess.GenericServer

  @impl true
  def init() do
    %{}
  end

  @impl true
  def handle_call({:get, key} = _message, state) do
    result = Map.get(state, key, "not found")
    {result, state}
  end

  @impl true
  def handle_cast({:put, key, value} = _message, state) do
    Map.put(state, key, value)
  end

  @impl true
  def terminate() do
    :ok
  end

end
```

We use the `@behaviour` key to use our `ServerProcess.GenericSServer` behaviour.
We implement the callbacks mentioned and make use of pattern matching on the 
messages we are receiving.
- `init()`: Initiates our process with map that is placed in the process state.
- `handle_call({:get, key} = _message, state)`: receives a `{:get, key}` message 
    to find the key in our `state` map and returns with the result and state.
- `handle_cast({:put, key, value} = _message, state)`: receives a message to put 
    a key in the state and returns the state.
- `terminate`: performs any cleanup.

Let's try this out in an iex shell
```bash
$server_process-> iex -S mix
iex(1)> alias ServerProcess.GenericServer
ServerProcess.GenericServer
iex(2)> alias ServerProcess.ExampleModule
ServerProcess.ExampleModule
iex(3)> pid = GenericServer.start ExampleModule
#PID<0.151.0>
iex(4)> Process.alive? pid
true
iex(5)> GenericServer.cast pid, {:put, :lang, "Elixir"}
{:cast, #PID<0.147.0>, {:put, :lang, "Elixir"}}
iex(6)> GenericServer.call pid, {:get, :lang}
Elixir
:ok
iex(7)> GenericServer.stop pid, :normal
{:stop, #PID<0.147.0>, :normal}
iex(8)> Process.alive? pid
false
```

We are able to run all our functions.
This brings us to the end of the blog.

-----------------------

## Summary
We implemented the GenServer Behaviour ourselves, we created it a manner that
heavy lifting of initializing server process and maintaining the state is 
handled by generic code, whereas the specifics are being handled by using 
callback module. The callback module implements callbacks we created in the 
behaviour. Although, we can create such an abstraction ourselves it's important to 
use the abstractions provided by OTP as they are much more stable and provide 
other functionalities which are crticial for our application.


[behaviour]: https://elixir-lang.org/getting-started/typespecs-and-behaviours.html
[tail_recursion]: https://blog.appsignal.com/2019/03/19/elixir-alchemy-recursion.html
