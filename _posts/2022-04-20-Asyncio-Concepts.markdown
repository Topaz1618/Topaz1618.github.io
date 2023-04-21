---
title: "Asyncio Concepts You Should Know Before Using It! "
layout: post
date: 2022-04-20 19:04
tag:
 - Asyncio
 - Principle
 - Development
 - Python
blog: true
author: Topaz
summary: "Introduce some random thing of Flask"
permalink: mac-tools
---


<h1 class="title"> Asyncio Concepts You Should Know Before Using It! </h1>

<h2> Table of Contents </h2>

- [1. High-level overview of Flask](#c1)
- [2. Even Loop](#c2)
- [3. Method introduction](#c3)




<h2 id="c2"> 2. Even Loop </h2>
Even loop is neither a process nor a thread, but rather an event loop, which is a lightweight mechanism for managing and scheduling coroutines and tasks in a single thread.

The event loop is responsible for scheduling and executing coroutines and tasks, which are Python functions that can be paused and resumed in a non-blocking way. When a coroutine encounters an await statement, it yields control back to the event loop, which can then resume another coroutine or perform other processing until the awaited operation is ready to resume.

The event loop is implemented as a class called AbstractEventLoop, which is the base class for all event loop implementations.

But the event loop is more complex system that includes various components and mechanisms for managing and scheduling coroutines, handling I/O operations, and processing system events that AbstractEventLoop in asyncio library does not define. These components and mechanisms typically implemented in the concrete event loop classes, such as asyncio.SelectorEventLoop, asyncio.ProactorEventLoop, or asyncio.DefaultEventLoopPolicy.

For example:
- SelectorEventLoop class uses the operating system's select() or epoll() mechanisms to wait for I/O events and signals, and then dispatches the corresponding event handlers or callbacks.
- ProactorEventLoop class, on the other hand, uses the operating system's asynchronous I/O mechanisms to handle I/O operations in a non-blocking way.

Overall, while the AbstractEventLoop class provides a basic framework for implementing an event loop, the specific components and mechanisms are typically implemented in the concrete event loop classes, which may vary depending on the underlying operating system and other factors.


Demo of create a instance of Even Loop
```
import asyncio
def run_long_task():
    await asyncio.sleep(10)


@app.route('/long_task')
async def long_task_api():
    loop = asyncio.new_event_loop() # create a new event loop instance  
    asyncio.set_event_loop(loop)  # set the event loop for the current thread
    loop.create_task(run_long_task()) # run the coroutine using the new event loop
```

Note
{% highlight markdown %}
- Mechanism: a specific method or process used to achieve a particular result or objective

- coroutine: special type of Python function that can be paused and resumed at certain points during its execution, allowing other code to run in the meantime.  Coroutines manages by event loop inclduing scheduling and execution, as well as I/O operations and other system events.
{% endhighlight %}




<h2 id="c2"> 2. Method introduction </h2>
