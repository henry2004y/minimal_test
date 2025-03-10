---
title: Passing Arguments
tags:
  - programming
categories:
  - Blog
author: Hongyang Zhou
---

The design of high level program structures is very challenging. One common headache for me is how to pass arguments between function efficiently.

## Case 1

Say you are implementing a module for handling boundary conditions for a CFD code.
As the code becomes more and more complicated, you will need to select between a bunch of available methods.
From the user side, he or she only needs to write down the wanted BC in the input file as a string, and the code should automatically select the right BC.
However, since most users will only use a limited amount of BCs, we as programmers should avoid any variables from one BC being set or used if it is not called by the user.
On a high level, we implement this unified API:
```julia
set_boundary(arguments)
```
and on the lower level, we need to call different functions for different BCs based on the input arguments:
```julia
if arguments[1] = 'a'
   set_bc_1()
elseif arguments[1] = 'b'
   set_bc_2()
end
```
Then here comes the problem: if there is one BC that needs a special input argument, e.g. `time`, how do you pass it?
The simplest solution would be pass all the special argument through the top level function one-by-one.
But if as you have more and more complicated BCs, the list of input arguments will just go crazy.

An OOP solution would be: on the high level API, there is only one argument that determines which class of methods to use;
inside the low level function, you set all the required variables as needed.
In other words, instead of passing needed variables, we seek global variables as needed.
The general BC can be defined as an abstract class, and each specific BC can be defined as derived/extended class.
