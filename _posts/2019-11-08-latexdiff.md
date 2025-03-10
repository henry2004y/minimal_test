---
title: Using latexdiff
subtitle: Compare and show the differences between files
tags:
  - latex
  - computer
categories:
  - Blog
date: 2019-11-08
author: Hongyang Zhou
---

Compare the differences between files is a tedious task. However, if you pick the right tool, it may become as easy as one line command.

For my first paper, I learned a little bit of using the package **trackchanges**. This package provides some basic commands of manually 
pointing out the removal, substitution and comments. It is good enough if you use it from the first place. What if I added some changes and
forgot to track them initially? Then probably you should turn to **latexdiff**.

latexdiff is an invaluable utility that makes it easy to markup and view changes made to the document. 
It definitely reduced my burden of having to read through two files simultaneously where it would be easy to overlook subtle changes like 
word substitutions and changing numbers or signs in an equation. My advisor, who is an expert in Perl, specifically pointed out that 
**latexdiff** is an excellent script written in Perl. I searched on Google and found the author named F.J. Tilmann; he is supposed to be a
scientist who happened to know Perl pretty well. And this script has been developed and maintained by him since 2004. You can check it out
[here](https://github.com/ftilmann/latexdiff). A basic introductino is provided on [Overleaf](https://www.overleaf.com/learn/latex/Articles/Using_Latexdiff_For_Marking_Changes_To_Tex_Documents).

I spent two hours on my first attempt to make it work, but I failed. Then my advisor and I sat down together and digged into the problem. 
After about half an hour, we finally figured out that the issue for my latex source file is that in the `lstlisting` environment where I
showed some demo codes for OpenMP, there is a single dollar sign $ in the text, which is interpreted as the math environment in Latex. 
Since that one $ is assumed to come in pair to mark the beginning and end of math input, **latexdiff** made a mistake all across the file 
trying to match that $ sign with some other one that comes later. Unfortunately for string parsing tasks, similar symbols like single quote, 
double quote that cannot be distinguished either the start or end position will be a nightmare.

Anyway, we finally found the problem. Actually, there is an option named `PICTUREENV` which can be used to exclude some kind of 
environments. However, it didn't work for me.
