---
title: "OS: The Thread Inside The Process"
date: 2023-07-05T21:50:43+07:00
draft: true
---

![Image alt](/images/process_thread.png)
###### *image by bytebytego*

## Intro

Thread and Process are topics that are widely discussed. The difference between the two cannot be compared apples to apples. If you read the title, you will find a clue that 'Thread' essentially lives inside the 'Process'.
You can't spawn a thread without running the process first. In this stories we only cover a big picture about thread and process.

## What is Process

A process is an instantiation of a program that is being executed by a processor. When we run a program or service, it creates a process, and the running program can have multiple processes within it, referred to as child processes.
These child processes are controlled by the main process.
Each process has its own virtual address space, executable code, processor ID, security context, and other attributes. However, these characteristics may vary depending on the operating system you are using, as each OS has its own mechanisms and implementations.

When a process is running, it can spawn one or more threads within it. Having multiple threads within a single process is beneficial when aiming for parallelism or concurrency in program execution.

## What is Thread

In simple terms, a thread can be seen as an execution unit within a process. It exists within the context of a process, which means that the operating system needs to be aware of the process before executing the thread.
Since each process has its own virtual address space, the threads residing within the process can share this virtual address space and system resources.
This allows threads to communicate and access shared data within the process, facilitating efficient coordination and resource utilization.

The operating system allocates processor time to execute threads within a process. This allocation is managed by the scheduler, which assigns priorities to each thread.
The scheduler determines the order and duration of execution for each thread based on these priorities.
By spawning multiple threads within a single process, we can achieve instruction concurrency, allowing multiple threads to execute instructions simultaneously, enhancing performance and responsiveness

## Thread(s) Per CPU Core

```
➜  ~ lscpu
Vendor ID:               GenuineIntel
  Model name:            11th Gen Intel(R) Core(TM) i7-11370H @ 3.30GHz
    CPU family:          6
    Model:               140
    Thread(s) per core:  2
    Core(s) per socket:  4
```

In the previous explanation, I mentioned that it is possible to have multiple threads running within a single process, how is this achieved?
You can see that each core can have a maximum of 2 threads, resulting in a total of 8 threads, what would occur if we were to spawn 100 threads within a single process?

To be honest, I was also confused at first. Apparently, the reason is that the thread we learned about previously is related to the OS thread, not the CPU thread. The OS thread resides within the process in the kernel, while the CPU thread represents the maximum capability of the CPU to execute threads simultaneously, typically on the same core.
CPUs do not care about the number of OS threads you spawn because the OS scheduler handles the task of assigning CPU threads to execute the OS threads.

### References:

- https://learn.microsoft.com/en-us/windows/win32/procthread/processes-and-threads
- https://learn.microsoft.com/en-us/windows/win32/procthread/scheduling-priorities
- https://learn.microsoft.com/en-gb/windows/win32/procthread/about-processes-and-threads?redirectedfrom=MSDN
- https://www.quora.com/What-is-the-difference-between-CPU-thread-and-OS-thread (answered by Herb Martin)
- https://blog.bytebytego.com/p/ep37-process-vs-thread
