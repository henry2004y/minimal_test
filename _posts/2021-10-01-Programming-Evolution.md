---
title: Evolution of Programming
tags:
  - programming
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2021-12-30s
---

In the 20th century as a programmer, you have to learn and write machine code.
In the 21st century, it is no longer the case.

Now you need to learn how to write do loops; in the future you may only need `map` and `reduce`.

## Automating Resource Management

Quoted from [Guy L. Steele Jr.'s talk at Strange Loop](https://www.infoq.com/presentations/Thinking-Parallel-Programming/) in 2011.

Keep adding levels of abstractions:

* Coding in octal or decimal
  * Original form, write strings
* Assemblers
  * Names for instructions
  * No longer care about the actual numerical values
* Relocating assemblers and linkers
  * Compiler is allowed to move instructions within memory and link them together
  * No longer care about where in the memory the program is placed
* Expression compilation
  * No longer care about the order of in which instructions are coded
* Register allocation
  * Automated by the compiler
* Stack management of local data
  * Automated by the compiler
* Heap management
  * No longer care about where my data is at
* Virtual memory / address remapping

## Next Level of Parallel Programming

* The best way to write parallel applications is not to have to think about parallelism.
  * Need for separation of concerns
* The issue is not so much parallelism as independence.
* Accumulators are BAD. Divide-and-conquer is GOOD.
* Certain algebraic properties are very important.
  * Associative: grouping doesn't matter 
  * Commutative: order doesn't matter
  * Idempotent: duplicates don't matter 
  * Identity: this value doesn't matter
  * Zero: other values don't matter
* For debugging, reproduceability is extremely important.
  * Worth sacrificing performance for

We need a different mindset for parallel prgramming:

* Good sequential code minimizes total number of operations.
* Good parallel code often performs redundant operations to reduce communications.

* Good sequential algorithms minimizes space usage.
* Good parallel code often requires extra space to permit temporal decoupling.

* Sequential idioms stress linear problem decomposition, i.e. process one thing at a time and accumulate results.
* Good parallel code usually requires multiway problem docomposition and multiway aggregation of results.

Think about `map, reduce` in the last bullet point.

Guy proposed a language level parallel paradigm where

* programmers define your data structures and methods;
* programmer ensure to the compiler that your data structures and methods maintains certain properties like associativity and commutativity;
* the compiler decides if it is possible to apply a parallel execution given the available resources on the fly.

Invariants give the implementation _wiggle room_, i.e. the freedom to exploit alternate representations and implementations.
In particular, _associativity_ gives implementations the necessary wiggle room to use parallelism---or not---as resources dictate.

So, in general, he envisioned an automated parallelism management. Brilliant!
