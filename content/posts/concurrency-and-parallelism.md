---
title: "Concurrency and Parallelism"
date: 2022-08-25T21:39:57+07:00
draft: true
---

## Intro
Parallelism and concurrency are topics that make a lot of programmers confused, and some programmers can’t figure out the difference between them, personally, this topic is not beginner friendly, but it can be nice if you could understand it deeply, back then when I code some project I don’t consider how the process is processed behind the scene, I don’t know how to optimize some process to be more fast and efficient. In this article, I will explain the difference between them and give you some examples of real case projects.

## Thread
Before we deep dive into the main topics, we need to understand what is the thread, the terms of the thread sometimes are confusing, in the high-level thread has 2 types, a physical thread, and an OS thread, a physical thread it's a thread of the processor’s core, sometimes called a counter register or hardware thread, it's the total number of threads that processor supported. OS thread is a thread that is managed by OS, an OS may create thousands of these but it will only be able to simultaneously run up to the number of physical threads or counter registers.

![Image alt](/images/thread_img.png)

At a high level, the thread is a path of process execution, it’s the smallest sequence of programmed instructions, basically, a “process or tasks” will be executed by a thread, and 1 “process” can have more than 1 thread, the os thread is managed by OS scheduler. A single core of the processor can only execute 1 thread at a time, so when we have multiple threads the other thread will wait until the first thread is successfully executed. Nowadays some processors have feature hyperthreading, it's a feature that doubles the number of threads in the single-core processor to act like a multicore.

## Concurency
Concurrency is a process that runs 2 or more tasks at the same time (not literally), when we have only a single core processor how do perform async action to run the process simultaneously? Remember in the thread explanation that a single-core CPU can only process 1 task at one time, so how to achieve that action?  the answer is using concurrency, concurrency will interleave the operation of processes, or basically overlapping thread to another thread in a time period to act like a parallel process.

![Image alt](/images/concurency_img.png)

It shows that we have 2 tasks on a single-core processor, that tasks are run simultaneously but do not perform pure parallelism, the CPU is overlapping 1 task to another to act like parallelism.

Concurrency is used when the process is I/O bound, this is useful when some thread is waiting for an I/O response while executing another thread, the example is a network request when we call a rest API.

## Parallelism
Parallelism can perform the execution of 2 or more tasks simultaneously, what that means is we can run more than 2 tasks in a different thread at the same time, parallelism needs to run in different cores to achieve pure parallelism.

![Image alt](/images/pararelism_img.png)

Task 1 and 2 is processed in the different core, from the beginning the CPU only focuses on one task, and does not interfere with another task.

In real use cases, parallelism is used when the process is CPU bound like when compressing an image, rendering a video, and a heavy CPU usage.

It is possible to do concurrency and parallelism at one time? Yes, it's possible, when we have a lot of threads running the OS will schedule the execution depending on the number of CPU cores available.

![Image alt](/images/pararelism_and_concurency_img.png)

sources:
- geeksforgeeks.org/difference-between-concurrency-and-parallelis
- stackoverflow.com/questions/1050222/what-is-the-difference-between-concurrency-and-parallelism
- krivalar.com
- jenkov.com

Thank you for reading my article.
