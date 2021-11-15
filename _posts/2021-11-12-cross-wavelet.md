---
title: Cross-Wavelet Transform
tags:
  - math
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2021-11-15
---

Before we talk about cross-wavelet transform (CWT)[^CWT_name], we need to understand wavelet transform (WT). Before we learn wavelet transform, we'd better have a good understanding of Fourier transform. Take a look at [wavelet transform]({{ site.baseurl }}{% post_url 2021-11-15-wavelet %}) before moving on.

The cross-wavelet transform (CWT) method is a technique that characterizes the interaction between the wavelet transform (WT) of two individual time-series.[^CWT_ref]

The CWT method allows us to measure

- the degree of synchronization phenomenon between different components
- the evolution over time of the times-series data set.

Similarity/differences between two time-series can be identified. If these behavioral states have different temporal evolution, the CWT method helps quantifying how different they are, and what the time lag between the two different behavioral states is. In short, this method is able to yield information on the spatio-temporal organization between two time-series.

[^CWT_name]: This acronym is also used for continuous wavelet transform, to be distinguished from discrete wavelet transform. In MATLAB, the `cwt` function refers to continuous wavelet transform.

[^CWT_ref]: There is a nice reference in the field of psychology, [The relevance of the cross-wavelet transform in the analysis of human interaction – a tutorial](https://doi.org/10.3389/fpsyg.2014.01566).

## Synchronization

If you calculate the linear correlation between two time-series, you will get one value for the whole data; if you calculate the "local" linear correlation, you will get a 1D line values; if you perform CWT, you will get a 2D spectrum, i.e. the correlation at a given frequency at a given time.

So far, the Hilbert transform seems to be a relevant method to detect the phase synchronization between two time-series providing that the components of the time-series possess the same frequencies. However, this method cannot be directly applied to the analysis of the phase synchronization between two plurifrequential components of a time-series. A plurifrequential time-series is composed of a time-series with multiple frequencies occurring in the same time as it is commonly the case in living systems. The CWT method would be able to deal with such plurifrequential time-series and is conjointly able to detect the phase synchronization of such time-series.

## Relative Phase

CWT can measure the ordering of the interaction among components, or in a continuous manner, lag.

## Requirements

- Same duration for the two time-series
- Similar sampling rate (downsampling may be useful if one of the time-series has a higher sampling rate than the other)

## Example

Here I repost the example given in the [referenced tutorial]((https://doi.org/10.3389/fpsyg.2014.01566)).
Two synthetic time-series were created. The two time-series, s1 (Figure 2A) and s2 (Figure 2B) have a duration of 102.4 s (with a sampling rate of 50 Hz). The amplitude of s1 and s2 is 1 arbitrary unit (a.u.). Both time-series have been divided into five intervals.

- For the first three intervals `[0–61.44]`, the time-series are composed of a high frequency component at 1 Hz and a low frequency component at 0.5 Hz.
- For the last two intervals `[61.44–102.4]`, the time-series are composed of a high frequency component (1 Hz) and an intermediate frequency component (0.75 Hz).
- 1st interval: characterized by a zero degree RP between s1 and s2 (the two time-series are identical in this interval).
- 2nd interval: the time-series s1 is characterized by a 90° phase lag in the high frequency component (1 Hz).
- 3rd interval: a 90° phase lag is applied on s1 in the low frequency component (0.5 Hz).
- 4th interval: a phase lag of 90° on the high frequency component and a phase lag of 180° on the intermediate frequency component are applied to s1.
- 5th interval: a phase lag of 180° on the high frequency component and of 90° on the intermediate frequency component are applied to s1 (see Table 1 for a summary of the changes made to s1).
- No phase lag exists in s2.

<figure>
    <img src="https://www.frontiersin.org/files/Articles/111259/fpsyg-05-01566-HTML/image_m/fpsyg-05-01566-t001.jpg"
         alt="modulation on s1">
    <figcaption>TABLE 1. Details of the phase lag and frequency modulations of s1.</figcaption>
</figure>

<figure>
    <img src="https://www.frontiersin.org/files/Articles/111259/fpsyg-05-01566-HTML/image_m/fpsyg-05-01566-g004.jpg"
         alt="cross wavelet transform demo">
    <figcaption>FIGURE 1. Wavelet transform analysis and cross-wavelet transform (CWT) analysis of time-series (s1 and s2). (A,B) are the representations of s1 and s2, respectively; (C,E) show the normalized Fourier spectrum of (A,B), respectively. In both cases, we can observe one main peak at 1 Hz and two other peaks at 0.75 Hz and 0.5 Hz. (D,F) are WT spectra (scalograms) of (A,B) respectively. These figures help to localize and quantify in terms of time of localization (time) and amplitude the frequency components previously identified in the Fourier spectra. In (D,F) two frequency components are present in the three first intervals of the time-series s1 and s2: one frequency at 1 Hz and one at 0.5 Hz. In the two last intervals, there are two frequencies: the frequency at 1 Hz, as in the three first intervals, and a new intermediate frequency at 0.75 Hz. (G) shows the cross-Fourier spectrum of (C,E). (H) Shows the cross-wavelet spectrum that is a representation of the common frequencies of (D,F) reflecting the local degree of interaction between the two analyzed time-series s1 and s2. (I) Shows the difference of phase of (A,B), i.e., the difference of phase between s1 and s2. The levels of gray permit us to visualize the associated relative phase (RP). a.u., arbitrary unit.</figcaption>
</figure>

## Discriminate the Properties of Time-Series

[Torrence and Compo, 1998](https://doi.org/10.1175/1520-0477(1998)079<0061:APGTWA>2.0.CO;2) have demonstrated that, *each point of the WT spectrum is statistically distributed as a chi-square with two degrees of freedom*. The confidence level is computed as the product of the background spectrum (the power at each scale) by the desired significance level from the chi-square ($\chi^2$) distribution. When the WT spectrum is higher than the associated confidence level it is said to be “statistically significant.” Following this statistical test, we can obtain what is usually known as *the cone of influence*, which is the region under the thin black lines in Figure 1. See the [MATLAB documentation](https://se.mathworks.com/help/wavelet/ref/conofinf.html) for a live example.

## Wavelets in Julia

The main package for wavelet in Julia is [Wavelets.jl](https://github.com/JuliaDSP/Wavelets.jl). Note that as of version 0.9.3, this package only supports discrete wavelet tranform. As an extension, [ContinuousWavelets.jl](https://github.com/UCD4IDS/ContinuousWavelets.jl) implements the continuous wavelet transform, with some examples of scalograms as well. I'm contacting the authors to see if it's possible to extend the package even further.
