---
title: Cross-Wavelet Transform
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
---

## Wavelet Tranform

Before we talk about cross-wavelet transform (CWT), we need to first understand wavelet transform (WT). Conceptually wavelet transform is similar to Fourier transform, but with the main difference that *wavelets are localized in both **time** and **frequency** * whereas *the standard Fourier transform is only localized in **frequency** *. This means that for a given time series, Fourier analysis gives you precisely the frequency magnitude and phase across the whole time interval, but you cannot tell when in time the signal is sounded. Wavelet analysis takes the temporal extent into consideration by sacrificing the accuracy of the frequency spectrum. The end result looks similar as if you perform a local Fourier tranform in a small time span around each time stamp (which is how the traditional spectrogram plots are done).

Some more detailed explanation can be found in this [Q&A](https://math.stackexchange.com/questions/279980/difference-between-fourier-transform-and-wavelets).

## Cross-Wavelet Transform

The cross-wavelet transform method is a technique that characterizes the interaction between the wavelet transform (WT) of two individual time-series.[^CWT_ref]

The CWT method allows us to measure

- the degree of synchronization phenomenon between different components
- the evolution over time of the times-series data set.

Similarity/differences between two time-series can be identified. If these behavioral states have different temporal evolution, the CWT method helps quantifying how different they are, and what the time lag between the two different behavioral states is. In short, this method is able to yield information on the spatio-temporal organization between two time-series.

[^CWT_ref]: There is a nice reference in the field of psychology, [The relevance of the cross-wavelet transform in the analysis of human interaction – a tutorial](https://doi.org/10.3389/fpsyg.2014.01566).

### Synchronization

If you calculate the linear correlation between two time-series, you will get one value for the whole data; if you calculate the "local" linear correlation, you will get a 1D line values; if you perform CWT, you will get a 2D spectrum, i.e. the correlation at a given frequency at a given time.

### Relative Phase

CWT can measure the ordering of the interaction among components, or in a continuous manner, lag.