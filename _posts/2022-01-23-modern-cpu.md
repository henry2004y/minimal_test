---
title: The Effectiveness of Modern CPUs
tags:
  - computer
categories:
  - Blog
author: Hongyang Zhou
---

While watching this Cppcon 2021 video

<iframe width="560" height="315" src="https://www.youtube.com/embed/g-WPhYREFjk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I learned many things from a low-level hardware perspective.

## Loop Unrolling

I realized why loop unrolling is a trick in speeding up the performance. Modern CPUs are extremely efficient in arithmetic instructions, that even register operations cannot compete, let along the levels of memory caches. This means that in practice in a loop like

```cpp
for(i=0; i<N; i++){
   a1[i] += b1[i] + b2[i]
   a2[i] += b1[i] - b2[i]
}
```

costs nothing for an extra line as long as the required data has been fetched into the register.

### Hardware Loop Unrolling

Be careful of the difference between compiler unrolling and hardware unrolling. Even after the machine code has been generated, it may still not show any unrolling. However, the next stage of the pipeline can still run even if the registers are still in use. Here is where the concepts of physical registers and conceptual registers come into play, and the result is hardware loop unrolling, also potentially out-of-order execution.

## Branch Prediction

CPUs try to predict the future from the past for branches. If most predictions are true, then great, you save a lot of time checking the dead-end part. However, if a certain amount of predictions are wrong, you pay for a price, sometimes even more than normal checking. The analogy is like while in a game you try to be smart to take the risk, but it turns out that all your previous effort goes to the trash can.

Here are some practical ratios. An efficient branch predictor usually has less than 1% of branch-misses; when this ratio increases to 10%, it slows down the code quite a bit (6x in the presenter's demo case!).

One funny story that happened in the past was one of the CPU vendors once had a branch predictor worked exactly the opposite as to what one expected. For this particular CPU, you could increase the performance by an order of magnitude if you act in a way to shut down the predictor.

Do not try to be smarter than the predictor. It can learn complex patterns that are difficult for humans to detect, especially that nowadays machine learning techiques have been adapted to these architectures.

Almost all ways to remove branches end up in more computations, so there is a trade-off. One trick that is commonly seen in Python/MATLAB/Julia is to use booleans as indexes. This improves performance if

* extra computations are small
* branch is poorly predicted

And the most important take-away message: always measure your performance with the powerful profilers before blindly trusting your judgement!