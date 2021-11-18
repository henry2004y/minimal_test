---
title: Fourier Analysis
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
---

I hate it when people make simple things complicated, and complicated things impossible.

Let's follow Mark A. Kramer's short course [An Introduction to Field Analysis Techniques: The Power Spectrum and Coherence](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwjb9rHbwqH0AhVtxosKHZggDuYQFnoECAQQAQ&url=https%3A%2F%2Fwww.sfn.org%2F~%2Fmedia%2FSfN%2FDocuments%2FShort%2520Courses%2F2013%2520Short%2520Course%2520II%2FSC2%2520Kramer.ashx&usg=AOvVaw1cR0HUTjm4MKtCBzvwwxDA). I also have a copy in my Google drive.

## Coherence

*Problem at hand*: brain recordings often consist of multiple sensors, and recent advances in recording technology promise observations of brain activity from many sensors simultaneously. How do we make sense of these large, simultaneous, multivariate recordings?

For simplicity, let's consider time series recorded simultaneously from two sensors during a task. To characterize these data, we compute the **coherence**. Coherence is a measure of the relationship between x and y at the same frequency. The coherence ranges between 0 and 1, \\( 0 \le \kappa_{xy,j} \le 1 \\), in which 0 indicates no coherence between signals x and y at frequency index j, and 1 indicates strong coherence between signals x and y at frequency index j.

\\[
\kappa_{xy, j} = \frac{|\< S_{xy, j} \>|}{\sqrt{\< S_{xx,j} \>} \sqrt{\< S_{yy,j} \>}}
\\]

where

\\[
\< S_{xy, j} \> = \frac{2 \Delta^2}{T} \frac{1}{K} \sum_k=1^K X_{j,k} Y_{j,k}^\ast
\\]

and \\( X_{j,k} \\) is the Fourier transform coefficient of \\( x_k \\) at frequency with index j.

We also call this the coherence in the frequency domain. One step further, we shall consider the coherence in the *time-frequency domain*, as mentioned below.

### Extension to non-stationary signals

If the signals are non-stationary, (and therefore not ergodic), the above formulations may not be appropriate. For such signals, the concept of coherence has been extended by using the concept of time-frequency distributions to represent the time-varying spectral variations of non-stationary signals in lieu of traditional spectra.

The **time-frequency coherence (TFC)** is defined to dealing with measuring the properties of nonstationary processes.

\\[
C_{xy}(t, f) = \frac{|\< S_{xy}(t, f) \>|}{\sqrt{\< S_{xx}(t, f) \>} \sqrt{\< S_{yy}(t, f) \>}}
\\]

For more details, check [Cross spectral analysis of nonstationary processes](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=53742).