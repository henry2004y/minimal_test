---
title: Fourier Analysis
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2023-03-22
---

> I hate it when people make simple things complicated, and complicated things impossible.

Let's follow Mark A. Kramer's short course [An Introduction to Field Analysis Techniques: The Power Spectrum and Coherence](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwjb9rHbwqH0AhVtxosKHZggDuYQFnoECAQQAQ&url=https%3A%2F%2Fwww.sfn.org%2F~%2Fmedia%2FSfN%2FDocuments%2FShort%2520Courses%2F2013%2520Short%2520Course%2520II%2FSC2%2520Kramer.ashx&usg=AOvVaw1cR0HUTjm4MKtCBzvwwxDA). I also have a copy in my Google drive.

## DC Offset

For many signals which involve large DC offsets, a raw FFT would often result in a big impulse around frequency 0 Hz, thus masking out the signals of interests with relatively small amplitude.

<img src="http://blog.originlab.com/wp-content/uploads/2015/03/Remove_DC_Offset_Blog_10.png" alt="DC offset">

There are two methods to remove DC offset from the original signal before performing FFT:

* Using FFT High-Pass Filter
* Subtracting the Mean of Original Signal

Alternatively, you can erase the power in the 0 Hz band after performing FFT.

## Coherence

*Problem at hand*: brain recordings often consist of multiple sensors, and recent advances in recording technology promise observations of brain activity from many sensors simultaneously. How do we make sense of these large, simultaneous, multivariate recordings?

For simplicity, let's consider time series recorded simultaneously from two sensors during a task. To characterize these data, we compute the **coherence**. Coherence is a measure of the relationship between x and y at the same frequency. The coherence ranges between 0 and 1, \\( 0 \le \kappa_{xy,j} \le 1 \\), in which 0 indicates no coherence between signals x and y at frequency index j, and 1 indicates strong coherence between signals x and y at frequency index j.

\\[
\kappa_{xy, j} = \frac{|\< S_{xy, j} \>|}{\sqrt{\< S_{xx,j} \>} \sqrt{\< S_{yy,j} \>}}
\\]

where

\\[
\< S_{xy, j} \> = \frac{2 \Delta^2}{T} \frac{1}{K} \sum_{k=1}^K X_{j,k} Y_{j,k}^\ast
\\]

and \\( X_{j,k} \\) is the Fourier transform coefficient of \\( x_k \\) at frequency with index j. k is the trial index, and K is the total number of trials of data from each sensor.

We also call this the coherence in the frequency domain. One step further, we shall consider the coherence in the *time-frequency domain*, as mentioned below.

## Time-Frequency Coherence

When calculating coherence we assume stationary signals. If the signals are non-stationary, (and therefore not ergodic), the above formulations may not be appropriate. For such signals, the concept of coherence has been extended by using the concept of time-frequency distributions to represent the time-varying spectral variations of non-stationary signals in lieu of traditional spectra.

The **time-frequency coherence (TFC)** is defined to dealing with measuring the properties of nonstationary processes.

\\[
C_{xy}(t, f) = \frac{|\< S_{xy}(t, f) \>|}{\sqrt{\< S_{xx}(t, f) \>} \sqrt{\< S_{yy}(t, f) \>}}
\\]

There are usually two representations of the analysis in time-frequency domain:

- analytic signal resulting from Hilbert transform
- wavelets

Here we talk about the analytic signal approach.

### The analytic signal

The analytic signal representation of time-series x has the form \\( z = x + iy \\), where y is the Hilbert transform of x. z is a complex signal in the time domain with the same sampling rate as the original signal. By applying a filter bank to the signal, that is, a series of band-pass filters centered at successive frequencies f, and by computing the Hilbert transform for each filtered signal, we obtain the analytic signal in the time-frequency domain, that is, for all points \\( z_{t,f} = x_{t,f} + i y_{t,f} \\) in the time-frequency plane.

#### Bivariate Measures in the Frequency and Time-Frequency Domain

Bivariate means between two variables, or two time-series. Two popular measures are *coherence* and *phase-coherence* (also known as *phase-locking value*).

For any analytic signal \\( z=x+iy \\) at a time-frequency point or region, the **auto-spectrum**

\\[
C = z z^\ast = x^2 + y^2
\\]

is a real quantity providing the squared amplitude (power) of the signal, i.e. the time-frequency domain equivalent of the signal variance, which is a natural measure of the signal energy. Given two analytic signals \\( z_1 \\) and \\( z_2 \\) in the time-frequency region, the **cross-spectrum** between them is the time-frequencyy domain equivalent of their covariance and is given by

\\[
C_{12} = z_1 z_2^\ast .
\\]

The coherence measure is defined as

\\[
\kappa = \frac{|\< C_{12} \>|}{\sqrt{\< C_1 \>} \sqrt{\< C_2 \>}},
\\]

which is the equivalent of Pearson's correlation in the time-frequency domain, taken in its absolute value.

## Tools

### Julia

There is a [FourierAnalysis.jl](https://github.com/Marco-Congedo/FourierAnalysis.jl/tree/master) package. I tried to follow what are available inside that package: it's very complicated. The author tries to be thorough in the documentation, but it's far from perfect.

There is another one, [SignalAnalysis.jl](https://github.com/org-arl/SignalAnalysis.jl), which I used to generate spectrum before. I need to learn more about the pros and cons between these packages.
