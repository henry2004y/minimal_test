---
title: Software Installation
subtitle: Where we can do better
tags:
  - computer
categories:
  - Blog
last_modified_at: 2022-04-15
---

The first thing to tell if one is familiar with codes is whether he or she can compile it. As I move to new group using a completely different code, this is a good chance to learn the workflow.

## Design

I would say Vlasiator chose a very different approach compared to SWMF, the latter of which has everything bundled together as a stand-alone package. I love the design that there is no absolute path in the config or Makefile. Meanwhile I don’t like the choice of Makefiles set by an environment variable, neither do I enjoy reading the installation guidance with pieces of information here and there. The lack of help information is really a big barrier for new users.

Honestly this is the first time I’ve ever looked into the details of C++ package dependencies. I spent some time understanding which files/packages should be in the lib paths, and which should be in the include paths. I looked at how Vlasiator handles all the dependencies in the Makefile as well as the parameters, and thought there might be a way to greatly simplify the installation process. At this point I still don’t know how Vlasiator chooses which problem to solve, but that should be an easy one.

Here is a list of issues that I found:

1. When using git for an open source project held on GitHub, we should usually access through `https` but not `ssh`.
2. Overall the dependencies are too complicated to handle manually. I have been working with scientific codes for years now, but it still took me quite a while to install all the dependencies.
3. The installation guide wiki page is not easy to follow. I would be amazed if any outsiders successfully compile the code by following the guidance.
4. Changing all the parameters directly in the Makefile is not a good idea; explicitly listing all the compilation dependencies is also unnecessary and unmaintainable for large C++ projects.
5. Source codes should be better organized by folders.

I remember during the past few months at Michigan, I complained quite a bit about how BATSRUS can utilize the modern workflow better and become more productive. I would not realize the actual situation in other scientific groups if I did not move. This is the process from which I learn.

Finally after more than two days of work, I got something that works as I wished. Similar to the Config.pl file in SWMF, the new configure.py file is used to set the parameters and library paths before compilation. Alternatively, one can just type `-install` to make a fresh installation. This file is borrowed from the Athena++ code at Princeton with many modifications. I would say in the world of scripting, the most commonly used ones I see are Python, Perl and Shell. Exactly which one is used is highly dependent on the time it is written. Either one will work if you put enough effort, but it is nice to at least have a taste for each of them. As a fan of Julia, even though I really want to use it here, I know it will be slow and unportable at the current time without an interpreter. As a rule of thumb, understand your goal and choose wisely according to your needs.

Meanwhile, I also tried to reorganize the code placement to make it look better. I don’t know if other guys will like it or not; let’s just wait and see.

The current situation at the Vlasiator group reminds me of the potentially similar one happened at CSEM 20 years ago, when Gábor were recruited by Tamas and became the leader in code development.
That is good and bad, in some sense. We are no longer in the world 20 years ago.
Yesterday when I was wandering around the campus, I saw the computer science department right below my office, with the introduction of Linus Tolvards everywhere.
If one truly believe in the concept of open-source, make it easy to install the code is the first step, and then collaboration and improvements will come as a group effort.
Admittedly it would still be a practical issue to figure out how to avoid unnecessary competition and "stealing", especially when it comes to fundings. But I have faith.

## Progress

It turns out that a real compatible work takes me not two days but two weeks. After getting the advices from group members, I realized that totally abandoning the previous customized Makefile workflow is not ideal, and I have spent quite some time coming up with a proper way of setting those library paths in the main Makefile as well as customized ones back and forth. Keeping codes that are presumably useful in the future is also not a good idea, which is something I have read about a couple of months ago, and now it goes into practice.

So now on a fresh new machine running Ubuntu, one can simply do

```shell
./configure.py -install
make
```

to generate the binary executable if Boost is already installed in the default location. This can be polished better if more user cases are collected, but at this point I don't have to do anything further. Loading and saving customized Makefiles are also plausible, but additional improvements are only possible if anybody uses and complains.

The golden rule for me as an open-source project: if it cannot be compiled and run the first time, don't waste your time on it unless there is really something interesting underneath!

Package dependency is a hell. For example, if you install one of the most famous plotting packages nowadays matplotlib using pip, you need to not only say `pip install matplotlib`, but also other packages if you want to display the figures on the screen, otherwise by default it will use the `agg` backend with no display. That is why some people recommend installing it using `apt-get` under Ubuntu/Debian. However, the system-wise package management system seems to have some issue recognizing my self-installed version of python3.9 instead of the system default python3.6. Lately people recommend using Conda to handle all Python dependencies, and indeed I have better experience with conda. Nothing is perfect.

Next step is obviously testing. This is kind of missing for Vlasiator. Currently the existing part uses shell script to do testing, but I would say either the shell scripts need improvement, or they should be replaced with Makefile or other scripting languages. Let's wait for the next chapter.
