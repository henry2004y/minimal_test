---
title: Kinetic Plasma Simulations
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2021-12-19
---

昱曦向我推荐了一个讲解动力学模拟的讲座

<iframe width="560" height="315" src="https://www.youtube.com/embed/I09QeVDoEZY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/R7ymipAjQR8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

这里整理一些笔记。

## What Are We Missing From Fluid Descriptions

* Free energy contained within each fluid species due to interactions is completely lost due to the fact that we only use one bulk velocity to describe each fluid. For example, two counter-streaming plasma beams have average velocity 0 under the fluid descriptions, neglecting the fact that particles can turn due to collision or other types of interactions.

* The counter-intuitive fact about collisions in plasma: *the more particles we have within the Debye cube, the less collisional the plasma is*. Collisions in plasma usually happens in Coulomb interactions. Remember what's special about plasma is its collective behavior, and the Debye length is the physical length scale at which quasi-neutral charge particles can be considered as a whole that effectively shielded far-distance Coulomb interactions. From the perspective of one particle, the more charged particles I have around me, the more neutrality it is and then it would be easier for me to move freely in space.

* In fluid descriptions, if you set up a pulse which has all sorts of wave lengths in it, as it propagates, all wave lengths travels at the same speed. On the contrary, if you turn to kinetic descriptions, you can both get physical and numerical dispersions, which essentially means that different waves travel at different speeds, and you will see wiggles behind the initial pulse because the short wave lengths do not travel as fast as the long wave lengths.

## Caveats in Understanding PIC

* It is not correct to think macro particles as real particles. Otherwise you will fall into some niche corners: for example, what will happen if relativistic effects are taken into account? The faster the particle moves, the smaller its mass is? The proper way to think of macro particles is that it is a way of sampling the velocity space distributions at a given (x,y,z,vx,vy,vz) locations. Remember macro particles are not treated as delta-distributed in space: it has a finite shape function within a distance, which is equivalent to a group of particles with the same velocity distributed within a small spatial location. The key benefit in doing this is to avoid the strong short-distance Coulomb interaction (e.g. no large angle scattering) between point particles while maintain the long-distance Coulomb interaction between groups of particles as a whole, or in other words, more plasma-like.

* A more mathematical description of the interaction between the grid and the macro particles involves the words *scatter* and *gather*:
  * a *scatter* operation deposit particle charge on mesh
  * a *gather* operation interpolate fields to particle position and compute the force
  * **momentum conservation** requires that the *scatter* and *gather* operation are symmetric, so most of the time we use the same interpolation scheme for these two operations.

* The biggest contraint on the leap-frog scheme is that the discrete time step is limited by the electron plasma frequency \\( \omega_p \\):
  * if \\( \omega_p \Delta t \le 2 \\), the scheme is stable, but suffers from phase error;
  * if \\( \omega_p \Delta t \le 2 \\), there will be an anti-damping component (i.e. negative imaginary part following the usually sinuisoidal perturbation expression) in the frequency which makes the scheme unstable.

* Be careful in evaluating the implicit time-stepping versus explicit time-stepping. A numerical solver, no matter how elegant it is, should not over-shadow the original equation. Simplicity and understandability are more important than performance.

* Noise: low-order interpolation creates spikes and in turn shows up as noise in the electric field.

* Filtering of shape functions: the more "compact" the shape function is, the more higher k (short wave length) power it has. The convolution of two shape functions is equivalent to the multiplication of their Fourier transforms. Therefore the smoothing of real space shape functions acts as smoothing in the frequency space.
  * This is important because the discretization step introduces a correction term to the actual wave numbers and it acts to dampen the high k modes. If not damped, these high frequency wave modes will not disappear; instead, they show up as **aliased** low frequency modes on your discretized grid which has nothing to do with real physics! This usually leads to extra **heating** in the system; in worse situations, it may even distablize the system!
  * If Debye length is not resolved on the grid, **aliasing** will heat up the plasma until Debye length is resolved. This is known as numerical heating.

* An alternative way is to increase the number of macro particles: the spikes of low order interpolation will get averaged out. However, the problem in doing this is that the ratio of the mean amplitudes of the fluctuations to the slowly varying component varies as \\( \frac{1}{\sqrt{n}} \\), and the effect of these fluctuations is greatly enhanced because our numerical model typically uses far fewer particles than are present in reality.

* **Decentering** is the key in many numerical schemes, e.g. leap-frog, constraint transport, divergence-free Yee grid. In terms of Yee grid, this dual mesh like discretization is very natural when you perform the integral of curvature calculations of the EM field.

* When solving the conversation equations especially the mass conservation equation, numerial dispersion is bad because when you get negative density you get stuck, but it is kind of alright for a EM field solver because negative electric field is still fine.

* Nature is hyperbolic but not elliptic. The Poisson equation, which is elliptic, acts as a constraint from the charge conservation. You can of course solve the Poisson equation directly (typically using FFT method), but that requires information from all over the domain. The nature does not need to know Poisson equation to evolve the charge and currents: conservation law comes in the first place! Parallelization, localization, whatever you call it---I don't need to call my mum 5000 km away everytime I want to make soup.