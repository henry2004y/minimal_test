---
title: PNG Compressor
tags:
  - visual
  - computer
categories:
  - Blog
link: https://tinypng.com
last_modified_at: 2022-02-23
---


I have tried an excellent PNG file compressor online: [TinyPNG](https://tinypng.com/).
Usually this can compress a figure by about 60% without lossing visually detectable quality!
This should always be used whenever possible.

Unlike JPEG, PNG doesn't typically have a lossy compression scheme. What they can achieve is something I haven't really found an alternative for.

I made a donation of $5 to them. Great work!

- `ffmpeg` has some built-in compressor when combining figures into movies, so it make less sense to compress each figure before passing them to `ffmpeg`.

This video explains many things about the differences between PNG and JPEG:

<iframe width="560" height="315" src="https://www.youtube.com/embed/0jNIYWBDULI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

So basically, if you can count the number of colors in your image (e.g. most scientific plottings), use PNG. Even though PNG supports 24-bit colors, 8-bit (256 colors) is enough for most scientific images. This is the most important trick used in TinyPNG. 