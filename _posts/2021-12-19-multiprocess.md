---
title: Parallel Programming Concepts
tags:
  - programming
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2021-12-19
---

很长时间我都没能准确理解多进程之间的工作关系，特来梳理一下。名词上可能不太准确，但是尽量争取概念上是对的。

## Execution Units

The smallest execution unit the computer system can handle is thread, which in principle can be even transferred between different cores. Old computers can only execute single thread from each single program on a single CPU core, so there is no need to distinguish between threads and processes.
With the advance of multi-core systems, we need to build the level of abtractions.

* Thread is now truely the smallest execution unit.
* A process can launch multiple threads among a number of cores *within one processor*.
* A program can have multiple processes on different processors.
* The abstraction makes it such that it is not a strict 1-to-1 mapping between physical processors and virtual processes. For instance, an MPI program can launch more processes than what is physically available on the system.

简单的比喻，一个多进程程序就像一个团队一起做一件事情：一个进程就是一个人，一个线程就是一个人可以同时做好几件事情。

## Standards

Be aware of the difference between a **protocol** and an **implementation**.

* **Message Passing Interface (MPI)** is a standardized and portable message-passing standard designed to function on parallel computing architectures.
  * MPICH, OpenMPI are implementations of MPI.
  * Even though originally only C, C++, and Fortran are supported, there are now bindings which are libraries that extend MPI support to other languages by wrapping an existing MPI implementation such as MPICH or Open MPI.
* **OpenMP (Open Multi-Processing)** is an application programming interface (API) that supports multi-platform shared-memory multiprocessing programming in C, C++, and Fortran.
  * OpenMP implementations are coupled into vendors' compilers, if they are claimed to support a certain OpenMP version.

Any low level parallel library needs to deal with the system. In C++, there is a standard library `pthread` which allows you to interact with system threads (which is not available in the high level OpenMP); In Julia, the standard library `Distributed` is a native implementation of one-sided communication between processes. The implementation details envolves many network concepts like hand-shaking, data packing/serializing/deserializing, etc.

## How to Get a Faster Multi-Processing Framework

Factors that affect the parallel system's performance:

1. network connection speed
2. checking procedures when establishing the connection, sending/receiving messages, and closing the connection
3. the size of data that requires communicating
4. the frequency of communication

From a software programmer's perspective, only the last two points can be controlled.

## 设计理念

在并行领域，实际程序的执行效率天差地别。这就好比说众人拾柴火焰高，然而三个臭皮匠还不一定顶得过一个诸葛亮：我们经常能见到所谓并行程序比串行程序还慢的情况。宏观上来讲，提高并行效率无非这么几条原则：

* 保证每个“人”有能力高效工作（efficient serial execution）；
* 保证每个“人”有活可干（load-balancing）；
* 减少不必要的开会，任务分配下去后，少量高效地沟通，最后一道汇总（reducing communication）。

并行程序就好比一家公司，成天开会的公司干不成活的。

## Modern Philosophy of Concurrency

Quoted from the Go language documentation:

> Do not communicate by sharing memory; instead, share memory by communicating.

Channel is a programming concept for implementing this idea. A channel in programming has two halves: a transmitter and a receiver. A channel is said to be closed if either the transmitter or receiver half is dropped.
