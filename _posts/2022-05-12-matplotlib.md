---
title: Matplotlib
tags:
  - programming
  - visual
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2022-05-12
---

Matplotlib is a great library, but there are still issues. Over 20 years of development and 3 major version releases, Matplotlib has evolved into a giant library.

## Issues

### Backend Support

Matplotlib comes with a bunch of [backend](https://matplotlib.org/3.5.0/users/explain/backends.html) graphic support. Different backends come with different dependencies, which may or may not work on your machine. However, most of the users only use 1 or 2 of them. Nowadays the Tk backend works pretty well and is available everywhere. In practice as a 3rd party tool developer we may just use it (or the Agg backend for non-interactive code) and drop support for all of the other backends (which complicate the code a lot for not much benefit).

### Ticks And Labels

These are some annoying little stuff that frequently show undesired behaviors here and there. There are two key concepts/classes: `Locator` and `Formatter`. `Locator` controls where the ticks are drawed, whereas `Formatter` decides the label formats. For example, sometimes the [labels will overlap with each other](https://github.com/matplotlib/matplotlib/issues/18757); sometimes the default format is really not what you want, so you need to tweak it carefully.

### Subfigures

Subfigures are considered as experimental as of Matplotlib v3.5. I happened to discover a bug related to the callback methods of `quiver` that will be fixed in v3.5.2.
