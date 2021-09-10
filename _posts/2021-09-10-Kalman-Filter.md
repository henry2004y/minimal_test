---
title: Kalman Filter
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
---

The first time I've heard about Kalman filter is during my exploration of image feature extraction.
A MATLAB demo shows how Kalman filter can be used to track passengers in a monitor video.
It was actually after a while until I realized that Kalman filter was originally proposed to solve the multi-sensor problem.

<iframe width="560" height="315" src="https://www.youtube.com/embed/mwn8xhgNpFY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<iframe width="560" height="315" src="https://www.youtube.com/embed/1dMdbKgJBOY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Example: GPS + sensor

Imagine you are driving a vehicle.
Let the average of position given by GPS signal be z1, and the standard error square be σ1.
A gaussian distribution G(z1,σ1) is used to describe the distribution of actual position.

Onboard you also have a sensor that gives G(z2,σ2), which may be different from the GPS outputs. The question is: how to estimate the actual position?

Kalman discovered the most optimal estimation to be
\\[
z = \frac{\sigma_2^2}{\sigma_1^2 + \sigma_2^2} z_1 + \frac{\sigma_1^2}{\sigma_1^2 + \sigma_2^2} z_2
\\]