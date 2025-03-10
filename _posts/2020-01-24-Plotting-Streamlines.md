---
title: Plotting of Streamlines
tags:
  - visual
categories:
  - Blog
last_modified_at: 2022-04-15
---

The built-in streamline function of Matplotlib/MATLAB is not proper for scientifically visualizing field information.
一开始在MATLAB上开发VisAna的时候，记得Gabor就在说怎么这个磁力线莫名其妙地断了，旁边贾老师说这是MATLAB画图导致的问题，不是我程序写错了。后来转战Julia，改用Matplotlib，这个问题不但没有解决，还变得愈演愈烈。

问题的表现有几点：

1. The only control parameter for streamlines are `density`, which indicates that there are algorithms behind the scene to select and remove extra lines in a dense region.
2. Therefore there are lines ending for no physical reason, which is not suitable for scientifically interpreting the field data.
  
Take a look at the official streamline plots:
![matplotlib_streamline](https://matplotlib.org/3.1.1/_images/sphx_glr_plot_streamplot_001.png)

This is definitely what you expect. I noticed that there is a `minlength` argument described in [streamplot](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.streamplot.html). Maybe give that a try?

The solution turns out to be doing tracing alone. In [SpacePy]((https://github.com/spacepy/spacepy/blob/master/spacepy/pybats/trace2d.py)), there's a standalone C code for tracing one line at a time and output the point data. Initially I copied the C code, compiled it into a dynamic library, and then call the function directly from Julia. Next I incorporate the function into the plotting function I have and use it instead of the original streamplot in Matplotlib.

Maybe in Matplotlib 3.6, we can get a new keyword that provides continuous streamlines.

---

2D stream tracing is not that hard. Essentially this is just solving ODEs with the chosen numerical schemes. Let's start with a fixed stepsize and move on to an adaptive stepsize (which is what's being used in BATSRUS). More complicated stuffs include tracing in 3D general mesh. Work in progress.

One example in Python is given in this [post](https://pythonmatplotlibtips.blogspot.com/2017/12/draw-flow-with-continuous-stream-line.html). Essentially it is solving ODEs and connect points into lines. At each step in the discretized equation, a interpolation function is provided to calculate the vector field direction at a given point.

I have built a small package [FieldTracer.jl](https://github.com/henry2004y/FieldTracer.jl) for tracing streamlines and fieldlines in 2D and 3D. It is now almost feature complete. One specific reason I need this is to select fixed seeds for animations, such that there won't be sudden jumps of field lines between frames.
