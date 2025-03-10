---
title: Green's Function
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/ism2SfZgFJg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

最早在数理方程的时候接触到格林函数，但一直是懵懵懂懂的。在实际的演算中几乎用不到，于是一直也没真明白。稍微回顾一下。

First of all， Green's function is used for solving ODEs with the form
\\[
Ly(x) = f(x)
\\]
where y is the unknown variable, f is the applied force/source function, and L is a linear operator.

Linearity is the crux of the idea: if we can decompose the solution into some kind of element solutions, then by combining them together we can get the final solution.
Dirac function \\( \delta(x) \\) is the "strange" function we define for representing the elemental solutions, usually in the form of \\( \delta(x-\xi) \\), where \\( \xi \\) is the location of the source and x is the location of influence.

The solution
\\[
y(x) = \int f(\xi) G(x;\xi) d\xi
\\]
only applies for homogenous initial/boundary conditions, i.e. each \\( G(x;\xi) \\) must satisfy the same initial/boundary condition.
This is the key reason why solving an ODE with Green's function cannot be applied to general problems. 