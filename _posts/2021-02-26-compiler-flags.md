---
title: Compiler Flags
tags:
  - programming
categories:
  - Blog
---

As programmers, we tend to be smarter than compilers. More often than not, we can't.
Nowadays, compilers comes with hundreds of flags to choose from.
To make lives easier, compiler developers provide some neat flags for most common optimizations, namely `-Ox` series.
I am not a fan of compiler flags, nor do I pretend that I can be smarter by compiling the code beyond the default compiler settings.
Recently I have been testing Vlasiator on multiple platforms. I am shocked by the differences caused simply by GCC optimization flags.
Here are a few things I have learned so far.

1. Standard optimization flags are safer

On my Ubuntu laptop with GCC 7.5 (which is pretty old for sure), the results are consistent with standard `-O0`, `-O1`, `-O2` and even `-O3` flags.
As promised, the default flags are guaranteed to work in most scenarios.

2. Be cautious about `-ffast-math`

IEEE standard is there for a reason.
`-ffast-math` is a higher level flag which consists of multiple low level flags to speeds floating point operations up by loosening the IEEE restrictions.
On one specific test, adding this flag gives me 10% speed boost at the cost of inconsistent results: 0.2% relative difference in plasma bulk speed values for one spatial cell running for 3600s simulation time.

3. Be cautious about avx instructions

In my understanding, the avx series of instructions simply means vectorization of more operations in the cache.
In the current Vlasiator Makefile, there is a `-mavx` flag together with specific vectorization class from an external C++ library.

In other recommended flag settings, many people mentioned the `-march` flag.
For example, `-march=native` tried to detect the CPU architecture you are compiling on and add hardware-specific instructions, mostly avx-related flags, to your program.

Surprisingly, this also affects the results quite significantly.
Although the speed improved by 10% beyond pure `-O3`, the plasma bulk speed difference is also about 0.2%.

4. Be careful about `-Ofast`

On top of the previous two observations, I need to be really careful about the most aggressive `-Ofast` flag, which includes `-ffast-math`.

## Vlasiator

However, even if I turn off all the optimization flags, Vlasiator still shows different result on different platforms.
This indicates that there are even more hidden facts underneath.
