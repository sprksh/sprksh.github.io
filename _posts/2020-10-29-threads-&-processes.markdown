---
layout: post
title:  "Threads & Processes"
date:   2020-10-29 00:25:35 +0530
categories: threads process
---
TODO: <https://slikts.github.io/concurrency-glossary/?id=system-threads-vs-user-level-threads-green-lightweight-threads>

There are kernel threads and user threads

Basically user space thread vs kernel space thread.

Green Threads are the same as User space threads. Kernel Thread is used interchangeably with OS thread.

Os threads are hard to use as the programmer or the code is responsible for preventing data related problems through locks and all.

Green threads are opposite to os threads and are managed by the language (the users of the kernel)

These threads only exist only in language and not in the os. Green threads are much simpler for the programmer, but their performance varies: many languages can't take advantage of multiple cores.

Difference between process and threads.

great link: <https://stackoverflow.com/questions/200469/what-is-the-difference-between-a-process-and-a-thread?rq=1>

good link: <https://stackoverflow.com/questions/15983872/difference-between-user-level-and-kernel-supported-threads>

Threads and processes are independent sequences of execution.

Threads work in same memory space while processes have separate memory spaces.

A process provides resources needed for execution of program. Each process starts with a single thread but can create more threads. Each process has:

1.  a unique process identifier (pid)
2.  virtual address space
3.  executable code
4.  open handles to system objects
5.  environment variables
6.  security context
7.  minimum and max set sizes
8.  priority class
9.  at least one thread

A thread is an entity within a process that can be scheduled for execution. All threads within a process share its virtual address and system resources. Each thread maintains its:

1.  exception handling
2.  thread local storage
3.  a set of structures system will use to protect the thread context until execution
4.  registers
5.  stack

**Threads share code, data and files but have their own registers and stack**

![](https://www.evernote.com/shard/s737/res/c99b8d7f-e86e-44c4-8b94-b26ed6a0ba78)

Three ways threads can be implemented:

1.  userspace thread
2.  kernel thread
3.  combination of two

**What is needed to implement thread:**

The ability to maintain cpu state and ability to maintain multiple stacks.

This can be implemented in userspace as well as kernel

User space thread switching is faster as it wont be trapped in kernel and you can schedule your thread the way you like.

The problem with user space thread is inability to perform blocking io call. why? i think it means you can do it but you dont want to do it because it halts all processes.

Kernel thread have the advantage of doing blocking io and let the os/kernel handle all scheduling related activity

Another approach: using multiple kernel threads each handling multiple user threads.

Blocking vs Non Blocking IO call:

blocking io: thread cannot do anything until io response is returned

non-blocking io: the io is queued straight away and function returns/ thread continues execution

**Can a single process run on multiple cores?: find answer in os & hardware architecture**

Yes. And that's for the kernel to take care.

cooperative multi-tasking, coroutines (<https://sprksh.github.io/notes/2020/04/18/multiprocessing-multithreading-cooperative-multi-tasking-coroutines-gevent.html>)

Best article on GIL: <https://opensource.com/article/17/4/grok-gil>

But what is runtime and what would python runtime be?

<https://stackoverflow.com/questions/60273813/what-is-runtime-in-context-of-python-what-does-it-consist-of/60274288?noredirect=1#comment106616451_60274288>
