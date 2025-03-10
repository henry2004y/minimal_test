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
It was actually after a while until I realized that Kalman filter became well-known after being successfully applied to the Apollo project for inferencing the satellite orbit.

<iframe width="560" height="315" src="https://www.youtube.com/embed/mwn8xhgNpFY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

* Kalman filter is used when
  * The variables of interest can only be measured indirectly. (Actually this applies to any measurement!)
  * Measurements are available from various sensors but might be subject to noise. (Again, almost always the case!)

<iframe width="560" height="315" src="https://www.youtube.com/embed/1dMdbKgJBOY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Example: GPS + sensor

Imagine you are driving a vehicle.
Let the average of position given by GPS signal be z1, and the standard error square be σ1.
A gaussian distribution N(z1,σ1) is used to describe the distribution of actual position.

Onboard you also have a sensor that gives N(z2,σ2), which may be different from the GPS outputs. The question is: how to estimate the actual position?

Kalman discovered the most optimal estimation to be a combined normal distribution with
\\[
z = \frac{\sigma_2^2}{\sigma_1^2 + \sigma_2^2} z_1 + \frac{\sigma_1^2}{\sigma_1^2 + \sigma_2^2} z_2
\\]
and
\\[
\frac{1}{\sigma^2} = \frac{1}{\sigma_1^2} + \frac{1}{\sigma_2^2}.
\\]

## Example: Navigation

Imagine you are sailing on the sea. At time \\( t_1 \\), you obtain the location \\( x_1 \\) and velocity \\( v_1 \\), such that you can estimate your new position after \\( \Delta t \\) at time \\( t_2 \\). Meanwhile, when you reach time \\( t_2 \\), you can measure your location and velocity again. What is then the optimal estimate of your actual location? Kalman filter.[^1]

[^1]: There is a bug for displaying LaTeX symbols like hat and widehat. I don't know how to fix it.

$$
\hat{x}_k = A \hat{x}_{k-1} + B u_k + K_k(y_k - C(A \hat{x}_{k-1} + B u_k) )
$$
is the posteriori estimate where
$$
\hat{x}_k^- = A \hat{x}_{k-1} + B u_k
$$
is called a priori estimate. \\( y_k \\) is the measurement at step k.

For the prediction part
\\[\begin{eqnarray} 
\hat{x}_k^- &= A \hat{x}_{k-1} + B u_k, \\\\
P_k^- &= A P_{k-1}A^T + Q,
\end{eqnarray}\\]
where \\( P_k^- \\) is the error covariance (which increases with steps). In a single state case, \\( P_k^- \\) is just the deviation of the prediction at step k.

These predictions are then fed to the update step
\\[\begin{eqnarray} 
K_k &= \frac{P_k^- C^T}{CP_k^-C^T+R}, \\\\
\hat{x}_k &= \hat{x}_k^- + K_k(y_k - C\hat{x}_k^-), \\\\
P_k &= (I - K_k C)P_k^-,
\end{eqnarray}\\]
where C is just a linear coefficent in the relation between \\( y_k \\) and \\( x_k \\):
\\[\begin{eqnarray}
x_k &= A x_{k-1} + B u_k + w_k, \\
y_k &= C x_k + v_k.
\end{eqnarray}\\]

For a more general form which includes nonlinearity,
\\[\begin{eqnarray}
x_k &= f(x_{k-1}, u_k) + w_k, \\
y_k &= g(x_k) + v_k.
\end{eqnarray}\\]
However, note that the new difficulty here is that after nonlinear transformation, the original Gaussian distribution at step k-1 may not be Gaussian anymore, such that the Kalman filter may not converge. One first idea to overcome this issue is to use the extended Kalman filter (EKF), which linearize the system at each step.[^2] Obviously this won't work well if the system is intrinsically highly nonlinear.

[^2]: This is where all the Jacobians, either analytically or numerically, come up. And most importantly, the system must be differentiable.

A better approach is called _Unscented Kalman Filter_(UKF). Instead of approximating a nonlinear function, UKF approximates the probability distributions. The filter selects a minimal set of sample points such that their mean and covariance is the same as the probability distribution, and are symmetrically distributed around the mean. Then after the nonlinear transformation, the mean and covariance of the transformed sigma points are used to calculate the new state estimate.

Another approach which is based on very similar concept is called _Particle Filter_ (or _Ensemble Kalman Filter_). It also uses sample points, referred as "particles". A significant difference between Particle Filter and Unscented Kalman Filter is that it approximates any distribution function, not only restricted to Gaussian. This also indicates that it needs much more particles than UKF does.

This is in essence exactly the same application as the human tracking in the monitor videos. You measure location and speed; you predict the next location; you measure again and correct the results. However note that in the latter application, Kalman filter enables tracking the same person who may goes out of sight for a short period, which is also why it significantly improves the result.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ul3u2yLPwU0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Example: Rocket throttle temperature measurement

We need to know the temperature at the center of the throttle \\( T_{in} \\) to control the fuel injection of the engine. However, the temperature is so high in the throttle such that it is impossible to put a sensor right inside the throttle. Instead, we have to measure it from outside and get \\( T_{ext} \\). How should we proceed?

With prior knowledge, we can build a mathematical model with input fuel \\( w_{fuel} \\) and output \\( \hat{T}_{in}, \hat{T}_{ext} \\). However, the mathematical model is only an approximation to the real system, which is subject to uncertainties. Our goal here is to eliminate the difference between \\( T_{ext} \\) and \\( \hat{T}_{ext} \\) so that \\( T_{in} \\) converges to \\( \hat{T}_{ext} \\). This negative feedback loop is called _state observer_.

<iframe width="560" height="315" src="https://www.youtube.com/embed/4OerJmPpkRg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Actually, Kalman filter is one kind of state observer designed for stochastic system.
