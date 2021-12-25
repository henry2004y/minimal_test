---
title: Blunt Body Problem in Hydrodynamics
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2021-12-25
---

At first sight, this seems to be an easy problem: there should be some kind of analytical solution given an ideal shape of the obstacle, say a cylinder in 2D or sphere in 3D. Unfortunately, there're no such simple solutions.

The introduction here is taken from *Time-Dependent Computation for Blunt Body Flows* by D. FREUDENREICH's master of engineering thesis.

## Definition

The **blunt body problem** consists of determining the flow field, including the location and shape of the *bow shock* formed, due to a *supersonic* flow around a body with a *blunt* leading edge.

The conservation laws which describe the motion of the flow are a set of nonlinear partial differential equations. The flow is supersonic ahead of the bow shock formed but subsonic behind it, up to sonic line, after which the flow is again supersonic, as shown in fig. 1.1; hence the problem is one of mixed supersonic-subsonic flows.

Another difficulty is that the boundaries in the problem are not completely defined, that is, only the shape of the body is known a priori. The other boundaries consisting of the bow shock and the sonic lines have to he determined as weIl as the flow properties in the region of flow.

{% include figure image_path="/assets/images/blunt-body-problem.png" alt="blunt-body" caption="The blunt body problem" %}

## Breakthrough Technique

It was not until G.Moretti and M.Abbett's idea that this becomes computationally feasible. The time-dependency of the method is based on considering the conservation equations for the problem in their unsteady configuration. In this form the governing equations are hyperbolic with respect to time, which therefore makes the technique very well suited for the unified analysis of mixed subsonic-supersonic flows.

The ana1ysis is started by assuming an arbitrary shock profile AB, as shown in fig. 1.2.

{% include figure image_path="/assets/images/hydro-shock.png" alt="hydro-shock" caption="." %}

The region that is analysed is that part of the flow field which is bounded by the assumed shock, the body surface, the plane of symmetry (AD in fig. 1.2) and an arbitrary upper boundary which must be in the supersonic region of the flow, that is, downstream of the sonic 1ine. The reason that the upper boundary shou1d be "innnersed" in the supersonic part of the f1ow, is to ensure that no distrubances are propagated back into the region under consideration; since for supersonic flows, disturbances are only propagated downstream. Thus referring to fig. 1.2, the region of flow represented by ABCD is the one under consideration.
This region is then divided into a network of points and the flow parameters inc1uding pressure, density and velocity are initia11y assumed for each point.

{% include figure image_path="/assets/images/hyperboloidal_body.png" alt="hyperboloidal" caption="." %}

Compressible fluid dynamics



{% include figure image_path="/assets/images/initial_shock_profile.png" alt="initial-shock" caption="." %}