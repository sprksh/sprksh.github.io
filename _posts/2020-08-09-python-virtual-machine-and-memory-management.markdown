---
layout: post
title:  "Python Virtual Machine & Memory Management"
date:   2020-08-09 00:25:35 +0530
categories: python
---
### Python vs CPython & other implemetations.

Language: It is just a language, the dialects, the expressions that constitute the language. Language notations for writing a program.

Implementation: The code that is written using that language is run by some interpreter

Bytecode: This is what is run by the virtual machine, JVM or PVM (Python virtual machine)

===================================

-   Python code > is compiled to cpython bytecode by **cpython virtual machine** > cpython interprets the bytecode
-   Python code > is compiled to java bytecode by jython > JVM interprets this bytecode.
-   Java code > is compiled to java bytecode which can be read by java virtual machine (JVM) > JVM interprets this bytecode > after 10000 runs, JIT compiler compiles java bytecode to machine code

So before JIT compilation, java is as interpreted as python. *But, Java bytecode is much better than cpython bytecode.*

Then why is python slower than java (even before JIT kicks in)

<https://stackoverflow.com/a/1732383/4510252> This answer is the coolest thing out there.

*In Python, an integer is a Python object with a bunch of attributes and methods attached to it. In Java, an int is a designated number of bits, usually 32. It's not really a fair comparison. Python integers should really be compared to the Java Integer class. Java's "int" primitive data type can't be compared to anything in the Python language, because the Python language simply lacks this layer of primitives, and so does Python bytecode.*

PyPy is the only python implementation which has JIT.

Understand how PyPy works/is different/faster

===================================

CPython: It is both an interpreter and a compiler. It compiles python to cpython byteccode which is then interpreted by cpython virtual machine to run.

More about cpyhton bytecode: <https://www.youtube.com/watch?v=cSSpnq362Bk>

### Cpython is a stack based virtual machine

Two types of VMs:

1.  Stack Based
2.  Register Based

<https://markfaction.wordpress.com/2012/07/15/stack-based-vs-register-based-virtual-machine-architecture-and-the-dalvik-vm/>

Basically, in stack based VM, we are pushing and popping values in/from stack while in register based VM, it works on pointers and has a lesser number of operations as compared to stack based VMs

Which one is better?: <https://stackoverflow.com/a/164190/4510252>

Cpython and JVM, both are stack based.

### Python Virtual Machine Components

Basic explanation: <https://www.youtube.com/watch?v=2Aknj_HHMtA> & <https://www.youtube.com/watch?v=aKoGwEPoQ5o>

Python virtual machine: major components

1.  Compiler: Compiles python to bytecode
2.  Interpreter: runs that bytecode
3.  Python Memory Manager: takes care of memory allocation, stack, heap, and reference counting
4.  Computation stack: (The stack in stack based VM)
5.  Garbage Collector (cyclic reference detector):

### Garbage Collection

There are in general two ways of garbage collection:

1.  Tracing: starts from root and traces all reachable objects. marks all unreachable objects and removes.
2.  Reference Counting: maintains ref count in runtime and removes object as soon as ref count falls to 0.

<https://rushter.com/blog/python-garbage-collector/>

Python's garbage collection works though reference counting. Reference counting happens in real time.

Additionally it has generational garbage collector which takes care of cyclic references. This is implemented through tracing. This does not happen in real time and the gc is run periodically. It is called generational because it is generation based.

Comparing to java, java does not have reference counting but it runs mark & sweep algorithm time to time. But in terms of time that is unreliable as it may stop normal functionality to run garbage collection which might give unreliable timings.

<https://stackoverflow.com/a/196487/4510252> has good overview.

*With basically all GC implementations there are trade offs but tracing is usually good for high through put (i.e. fast) operation but has longer pause times (larger gaps where the UI or program may freeze up). Reference counting can operate in smaller chunks but will be slower overall. It may mean less freezes but poorer performance overall.*

### Python Memory Manager

Python memory management is simple. It has 2 components:

1.  Stack Memory
2.  Heap Memory: (Private Heap)

Objects & instance variables are created in heap.

Methods & variables are created in stack.

<https://rushter.com/blog/python-memory-managment/>

Further Reading:

<https://leanpub.com/insidethepythonvirtualmachine/read>