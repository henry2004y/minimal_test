---
title: Kinetic Plasma Simulations
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2021-12-24
---

昱曦向我推荐了一个Anatoly Spitkovsky的动力学模拟的讲座，

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
  * There are schemes which carefully calculate the charge depositions and thus guarantee charge conservation. However, high order (>1) charge deposition schemes are ~ 25 times more expensive than 1st order?
  * Most PIC codes don't solve for Poisson equation: they assume charge conservation is valid throughout the time advance as long as the initial condition is charge conserved.[^FLEKS] There are two reasons behind this: 1. we assume this error is small that it does not affect the solution drastically; 2. Poisson solver is usually the most expensive part if included. If in the problem we are dealing with we do need to satisfy Poisson equation, in practice we do corrections every few time steps to as a trade-off.

[^FLEKS]: After years of fighting with numerical artifacts, Yuxi and Gábor came up with a Poisson equation correction to the semi-implicit PIC solver in FLEKS.

* What happens if we start with charge imbalance? For example, let's say we only initialize electrons in the domain and forget about ions. To satisfy Poisson's equation, the simplest solution would be zero electric fields. This means that the code thinks there is an opposite and equal charge density ion species to my initialized electrons. When electrons start moving, ions will sit on the grid. This is an easy way to simulate infinite mass of ions.

* High order FDTD schemes (4th spatial order) work better at reducing unphysical *Cherenkov instability*. Cherenkov instability relates to the dispersion error caused by the leap-frog scheme. Especially for relativistic particles, the dispersion error may create particles that travel faster than the speed of light **in the medium** (although still less than the speed of light in vacuum), which then becomes the recipe for the Cherenkov radiation. It's similar to the sonic boom of a supersonic aircraft.
Anatoly called this the "pedestrian" version because a strict analysis of the numerical Cherenkov instability also involves interaction with plasma modes, and the math quickly becomes "ugly".

* vPIC is known for its speed. Why can it achieve it and why doesn't all PIC codes follow? This is the art of balance, or trade-off: vPIC chooses to use the simplest schemes whenever possible and hope that all the statistical noises will be suppressed with enough number of macro particles per cell. As a comparison, the validity of a solution may be kept with 4 particles per cell with high order schemes and corrections, while vPIC may require 100 particles per cell; if vPIC is 25 times faster, then the final result is equivalent. However, one disadvantage I can think of about vPIC is that it then may require more memory/storage, which puts pressure on the hardwares.

* The asymptotic behavior of applying stencil filters N times is the multiplication of cosine functions. To think it in an easy way, cos(0) = 1 which gives the center value of the stencil (i.e. original value), and as this angle goes to \\( \pm 90^o \\), the weight factor goes to 0. Of course we can have negative factor values as well.

* Boundaries
  * Periodic: I am thinking if the periodic vectors in Julia can be easily used in our kernel codes without introducing ghost cells? Is it general enough to be compatible with other boundaries?
  * Perfectly conducting walls: tangential E --> 0, perpendicular B --> 0 (w.r.t. the wall boundary). A common problem arises in simulating a circle/sphere boundary in a Cartesian grid, where this stair-like inner boundary appears. By smoothing the electric field at the boundaries, which means that instead of strictly setting the transverse electric field to 0, we switch to kill part of it and hope it help smooth out the transition.
  * Open boundary: *absorbing layer*, *perfectly matched layer* (PML), transmitting wall. The idea of PML is to add a diffusive term to the Maxwell's equations, which you say that there is region on the grid with finite conductivity, which in turn damps out the field. This works like absorbing material with different conductivity for E and B fields. Another approach is the transmitting wall. This works well if the wave propagation direction is normal to the wall (i.e. no oblique waves), which is usually true if the wall is far away from the source. This is pretty cheap compared to PML.
  * Moving window, or a shift in the frame of reference, is sometimes used in beam and shock simulation. The simulation box is assumed to fly at the speed of light to follow a fast beam.
  * Injection of particles: we can have moving injectors, for example, in shock simulations.

* For PIC codes, usually parallel domain decomposition is not an issue, but **load balancing** is. For the typically case Anatoly showed, 90% of the time is spent with particles, and 10% of the time is spent with fields. The larger density gradients you have in your problem, the more severe the load balancing issue is. Shock and reconnection, the two main problems space physics studies, unfortunately fall into this regime.

* Emission of non-thermal radiation: most often the frequency of the energetic radiation is not resolved by the grid. If we care about radiation, we need to add photons as a separate species, and for example the radiation reaction force as an additional term in the equation of motion for pulsar simulations, or a force term for the inverse Compton scattering.

* The laser-plasma field is adding extra physics for high-intensity laser that will reach a fraction of the critical field when QED effects and pair creation become important.

* For black hole magnetospheres and pulsars, we need to consider general relativistic effects with non-Euclidian metrics.

* *Streaming instabilities*, like Weibel instability, can be important at collisionless shocks.

> Caution is required, but one can be paralyzed by a conservative attitude into missing profitable applications.
> --- Birdsall & Langdon (1991)

## A Few Words About Hybrid PIC

* An important limitation of full PIC methods is the limited separation of scales. Only microscopic systems can be modelled. In particular, it's hard to model electron/ions plasmas with realistic mass ratio, since \\( \omega_p \propto 1/\sqrt{m} \\), the real ratio will give a ~43 times difference. Hence ion acceleration is hard to capture with PIC (except in the ultra-relativistic limit).

* Hybrid codes treat ions as macro-particles and electrons as massless neutralizing fluid (methods works for non-relativistic plasmas), which means that in the electron momentum equation, we assume the electron inertia term \\( n_em_e\frac{d\mathbf{V}_e}{dt} \\) is 0, and the generalized Ohm's law can be derived based on this. Note again that in this simplified model electric field is now a state quantity that is determined by ion density, velocity, magnetic field, and electron pressure gradient.
  * Hybrid model is not accurate at the shock. The closure of the electron pressure term usually assume some kind of equation of state, which involves a **constant** polytropic index \\( \gamma \\) in \\( P \propto n^\gamma \\). The most common assumption is an adiabatic system, which is not true across the shock.
  * Ions are pushed at least twice in each timestep due to the requirement of proper centering in time for the EM fields. The numerical scheme looks a bit convolved because electric field and magnetic field are defined at staggered time stamps, but you need a future electric field to get a future magnetic field.
  * An interesting side effects is in the wave dispersion for whistler mode: \\( \omega \propto k^2 \\), so as you refine the grid more and more (i.e larger and larger k), the whistler goes faster and faster in an unbounded manner! In real physics, the whistler is bounded by electrons; since we don't have real electrons in a hybrid system, we cannot stop its growth. Therefore in practice we need to filter out the high spatial-frequency waves to make the code stable and hopefully lead to convergence. This is usually achieved by filtering the currents, but not fields. Anatoly said that's because it's much easier to maintain charge conservation when manipulating the currents. This also explains why in the older iPIC3D model filtering/smoothing the electric field does not end up doing anything better. However, if your scheme maintains charge conservation while filtering the fields, you should be fine.

* 我觉得混合模拟中的这套逻辑非常奇特。等离子体本来是一个整体，电子离子和电磁场共同作用形成一个体系。然而这里面的关系却是不对等的：粒子离开了电磁场就会变成杂乱无章的个体，但电磁场忽视一两个粒子对整个体系一点影响也没有。如果把离子比作中产阶级，把电子比作底层阶级，电磁场比作政府，那么混合模拟就好比我们只关心社会大尺度包括的问题，而仅把底层民众的声音当作一个背景声音，简化个体意志的影响。然而神奇的是，底层民众的行动依旧会被电磁场牢牢把持，甚至可以通过仅仅观察底层人民受影响的程度来映射整个政府的运作，而无需考虑中产阶级。我又想起了Y.Y课上做的社会比喻，那是一个起点，但整个模型的复杂程度可与描述社会相当。
