---
layout: post
title:  "Multiprocessing, Multithreading, Cooperative Multi-Tasking, Coroutines & Gevent"
date:   2020-04-19 00:25:35 +0530
categories: notes
---
First Read about GIL. This one is the best article: <https://opensource.com/article/17/4/grok-gil>

In multiprocessing, each process has its own GIL. In multithreading, they share the same GIL.

Basic intro of multi-threading and multi-processing: <https://www.youtube.com/watch?v=ecKWiaHCEKs>

Preemptive Multitasking: The normal hack played by CPU core to create the illusion of multiple processes running at the same time. Basically the CPU keeps on changing the program that is occupying the CPU after certain clock ticks so that all processes are not stuck due to a long running process and sometimes based on priority. So the CPU seems to be running multiple processes at the same time.

### Coroutines:

<https://stackabuse.com/coroutines-in-python/>

This is a good step by step article for basic introduction to coroutines.

Generators produce data while coroutines consume data. Coroutines like generators, maintain state.

In python multi-threading, CPU is aware of the thread and that makes it preemptive. (Python Threading is regular system level threading but bounded by GIL.) While coroutines are for non-preemptive cooperative multitasking. However at the CPU level, these processes are still preemptive.

Coroutines and concurrency are largely orthogonal. Coroutines are a general control structure whereby flow control is cooperatively passed between two different routines without returning.

Cooperative multitasking with coroutines: Given 2 functions, they can collaboratively switch control among each other manually where func A yields control to func B while it is waiting on io or otherwise. This is giving the control voluntarily. Note that in preemptive scenario, that is non-voluntary as the CPU and not the program is in control.

**This is a good table to get an overview of python's behaviour in different scenarios.**

|| **GIL** | **CPU Bound Tasks** | **Blocking IO** |
| **Multi Threading** | Separate thread but same GIL | Cannot run parallely | Blocking IO is not parallel within the thread. |
| **Multi Processing** | Separate process, so separate GIL | Can run in parallel. CPU handles it preemptively. | Blocking IO is not parallel within the thread of these processes. |
| **Coroutines. Asyncio** | Same thread, same process, same GIL | Nothing runs in parallel. | Can be used to make calls parallel. But manual effort in every call. |
| **Gevent Patching (Internally uses Coroutines only)** | Same GIL | Nothing runs in parallel. | Blocking IO becomes parallel (non-blocking) through monkey patching and event loop callbacks. |

### IO calls, Blocking IO vs non-Blocking IO.

It's simple. IO bound calls do not block CPU. But in a python process or thread, if the code is written in synchronous mode, the entire process will be on hold till the IO call returns. So, for the context of the python process, it is blocking. However, when multiple threads are running, the other thread can acquire the CPU while one thread is in waiting for IO. Basically Python GIL also performs preemptive multitasking.

Blocking IO is when the io process holds on to the socket till it gets the response

### Gevent, Eventlet & How Monkey Patching Converts things into Async

Vaidehi Joshi's great talk covering the width and depth. <https://www.youtube.com/watch?v=GunMToxbE0E>

**Green Threads**

They are threads in user space and are not created or managed by the OS.

Also they are cooperatively scheduled which means they are not scheduled or preempted by the OS.

Extremely lightweight as compared to threads and processes. Can concurrently run 20-30K green threads easily within a process. How true?

Gevent uses greenlet library underneath which is written in C.

```gr = greenlet(some_func)```
```gr.switch() # switches on or off the greenlet. Simple coroutine like result.```

But this is implemented in C and works by stack slicing/switching (at assembly level) of the C stack and python doesn't even gets to know about it.

Gevent also uses libev for callbacks in eventloops. Whats eventloop and callbacks. Separate topic.

By default TCP connections are blocking socket. Gevent Monkey-patching replaces socket with gevent.socket which is non blocking: `import ~~socket~~ gevent.socket`

So basically, to turn blocking into non blocking gevent monkey-patching uses libev eventloop. Rather using callback when response has arrived instead of holding on to the socket, blocking it.

### Let's get deeper into Gevent:

<http://www.gevent.org/intro.html#monkey-patching>

This is gevent's documentation. Go through this at least initial example to understand what's happening.

Using gevent in your application: <https://medium.com/@Pinterest_Engineering/how-we-use-gevent-to-go-fast-e30fa9f81334>

Another one: <https://medium.com/@ishay_13213/django-development-guidelines-by-initech-a0cd625c1cf8>

will get into these details in a separate note.

So Gevent is a coroutine based python networking library that uses greenlet to provide high level synchronous API on top of libev or libuv eventloop.

Basically in your application what you do is you is you monkey patch the entire process to make it not use the inbuilt socket but rather gevent.socket which eventually converts these things to non blocking which would have been blocking otherwise.