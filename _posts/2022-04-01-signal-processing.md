---
title: Practical Tricks in Signal Processing
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2022-04-09
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

Filtering is critical in signal analysis. Real data consist of signals of various frequencies, and we need to separate the them out. There are two basic classes of filters: the finite impulse response (FIR) filters, and the infinite impulse response (IIR) filters.

Unlike what I thought originally, filters are not all based on Fourier transform.

### High Pass Filter

- Often requires odd number of coefficients for the window. [StackOverflow](https://dsp.stackexchange.com/questions/18413/why-the-number-of-filter-coefficients-in-fir-filter-has-to-be-an-odd-number)

For example, in the [DSP.jl](https://docs.juliadsp.org/latest/contents/) package,

```julia
fs = 2.0 # sampling rate, [Hz]
responsetype = Highpass(0.1; fs)
designmethod = FIRWindow(hanning(63)) # high pass requires odd number FIR coeffcients
data_high = filt(digitalfilter(responsetype, designmethod), data)
```

In practice, usually what I need is an undistorted filter with fewer parameters. In that case, we may consider IIRs such as `Butterworth(n)`, where `n` is the filter order, and apply the filter twice (with the function commonly named as `filtfilt`) to cancel the delays. See [Caveats](#caveats) for more discussions.

### Low Pass Filter

```julia
responsetype = Lowpass(1.0; fs)
```

### Band Pass Filter

A combination of high pass and low pass filterings.

```julia
responsetype = Bandpass(0.1, 0.5; fs)
```

## Ordering of Steps

Smoothing is essentially one kind of low pass filterings. In practice, it is often better to

1. First demeaning (0th order) or detrending (1st order) with some kind of smoothers.
2. Then filtering the data in the band of interest.

## Caveats

Once when I checked the outputs from my low-pass filter, I noticed a phase shift with `filt`. Dr. Markus Kuhn explained the reason:

> The `filt` function applies a causal filter, i.e. one that cannot look into the future (a prerequisite for real-time processing). Therefore it can only react to its input with some delay --> phase shift. However there is a simple trick: applying such a filter twice on a signal that is already completely available in memory, once in _forward_ direction, and once in _backward_ direction, causes these delays to cancel each other out. The `filtfilt` function does exactly that for you. But keep in mind that it does apply the filter twice, i.e. it will double the filter order (make its transition steeper). The result is a non-causal filter (output samples will be affected by “future” input samples).
> 
> Other methods to obtain non-causal filters exist, for example filtering in the frequency domain, i.e. pad the signal, apply the FFT, attenuate some frequencies as desired, and then apply the inverse FFT and remove the padding (and boundary effects).If you use that method, never forget that the discrete Fourier transform operates always on a single period of a periodic signal, i.e. its last and first sample are neighbours. Hence the need for padding to separate them. The padding width should be at least the length of the impulse response of your filter.