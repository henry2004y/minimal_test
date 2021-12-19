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

这里整理一些笔记。

## What Are We Missing From Fluid Descriptions

* Free energy contained within each fluid species due to interactions is completely lost due to the fact that we only use one bulk velocity to describe each fluid. For example, two counter-streaming plasma beams have average velocity 0 under the fluid descriptions, neglecting the fact that particles can turn due to collision or other types of interactions.

* The counter-intuitive fact about collisions in plasma: *the more particles we have within the Debye cube, the less collisional the plasma is*. Collisions in plasma usually happens in Coulomb interactions. Remember what's special about plasma is its collective behavior, and the Debye length is the physical length scale at which quasi-neutral charge particles can be considered as a whole that effectively shielded far-distance Coulomb interactions. From the perspective of one particle, the more charged particles I have around me, the more neutrality it is and then it would be easier for me to move freely in space.

## Caveats in Understanding PIC

* It is not correct to think macro particles as real particles. Otherwise you will fall into some niche corners: for example, what will happen if relativistic effects are taken into account? The faster the particle moves, the smaller its mass is? The proper way to think of macro particles is that it is a way of sampling the velocity space distributions at a given (x,y,z,vx,vy,vz) locations. Remember macro particles are not treated as delta-distributed in space: it has a finite shape function within a distance, which is equivalent to a group of particles with the same velocity distributed within a small spatial location. The key benefit in doing this is to avoid the strong short-distance Coulomb interaction (e.g. no large angle scattering) between point particles while maintain the long-distance Coulomb interaction between groups of particles as a whole, or in other words, more plasma-like.
