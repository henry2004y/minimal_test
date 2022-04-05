---
title: Practical Tricks in Signal Processing
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2022-04-05
---


## Smoothing

Select the best smoothing methods that works for your data. Start from simple methods first.

Modified from [ADINSTRUMENTS](https://www.adinstruments.com/support/knowledge-base/what-are-advantages-and-disadvantages-various-smoothing-functions-available):

- Moving box averaging: rectangular window ("boxcar") averaged value, where each point has equal weighting.
  - simplest
  - fast even for very large window widths
- Median filter: the median filter sorts the data values in the window around each sample point and returns the middle value.
  - computation time \\( \mathcal{O}(n\log{}n \\), where n is the window width
  - effectively removes impulsive spikes from signals
  - It is recommended that a window width no larger than necessary is used.
- Triangular (Bartlett) window: triangular (Bartlett) weighting of the data points in the moving window which generates the smoothed values. With a triangular window the points in the middle of the window matter more, and the weighting decreases to zero going out towards the edges of the window.
  - reduces the effects of aliasing, and is often a good replacement for a traditional moving average.
  - faster than Savitzky–Golay smoothing, because computation time is independent of the window width. However Savitzky–Golay has the advantage that the amplitude of some high frequency components will be better preserved.
- Savitzky-Golay: fits a polynomial in a window of points around each sample point, using least squares fitting.
  - the degree of the fitted polynomial is usually between 2 to 6
  - computation time proportional to window width
  - has the advantage of preserving the area, position and width of peaks.
  - When data peaks are defined by only a few points, the Savitzky–Golay method flattens peaks less than moving average/triangular smoothing with the same window width. 

## Filtering

Filtering is critical in signal analysis. Real data consist of signals of various frequencies, and we need to separate the them out. One commonly used class of filters is the finite impulse response (FIR) filters.

### High Pass Filtering

- Often requires odd number of coefficients for the window. [StackOverflow](https://dsp.stackexchange.com/questions/18413/why-the-number-of-filter-coefficients-in-fir-filter-has-to-be-an-odd-number)

For example, in the [DSP.jl](https://docs.juliadsp.org/latest/contents/) package,

```julia
fs = 2.0 # sampling rate, [Hz]
responsetype = Highpass(0.1; fs)
designmethod = FIRWindow(hanning(63)) # high pass requires odd number
data_high = filt(digitalfilter(responsetype, designmethod), data)
```

### Low Pass Filtering

```julia
responsetype = Lowpass(1.0; fs)
```

### Band Pass Filtering

A combination of high pass and low pass filterings.

```julia
responsetype = Bandpass(0.1, 0.5; fs)
```

## Ordering of Steps

Smoothing is essentially one kind of low pass filterings. In practice, it is often better to

1. First demeaning (0th order) or detrending (1st order) with some kind of smoothers.
2. Then filtering the data in the band of interest.