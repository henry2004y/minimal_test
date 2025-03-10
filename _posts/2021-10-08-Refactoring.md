---
title: Refactoring
tags:
  - programming
categories:
  - Blog
author: Hongyang Zhou
last_modified_at: 2023-01-07
---

所谓refactoring，指的是在不改变程序外部功能的情况下对内部结构进行优化调整，

> to restructure software by applying a series of refactorings without changing its observable behavior.

逐渐我也开始遇到这类问题，希望借鉴一些历史经验。有一本书《Refactoring: Improving the Design of Existing Code》，作者是Martin Fowler和Kent Beck，最早出版于1999年[^1]。整理一些笔记于此。

[^1]: possible working link [here](https://d1wqtxts1xzle7.cloudfront.net/62290045/Martin_Fowler_-_Refactoring_-_Improving_the_Design_of_Existing-By_www.LearnEngineering.in20200305-13250-1kf3a2o-with-cover-page-v2.pdf?Expires=1633677046&Signature=eTZn3ibiwNbDeNqKr88Ckcmi1jxI2JLYBAgYgRidGaORO3DOv37o~~bP9eL2XP6BCp1xBcgddYrCWuxCm8P4Q-jee-fc6DSh~eUm7o27~sp58t6PjM7A2JAh2rAkukUoVbJ0kUBI1Gdmsop6U7psAw3zjOq2~2TTIkCXMLwLN~yhN229Y4OBvORW5BUPx2ax3Z4SUcn8-oe-kG0~6EkOGXSrmAlzVAABMov6q~bztY0z~GTbOF1fA75SLmh2rW59XY8QRGzA3vcm6UWPr84AjXOWN2bFuMJu01O2lVXz6v0RLQB3dpAwnqcFjuS8wRYM7J6HvYlB9tr4O2tuJamDfA__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA)

时刻记得Donald Knuth的话：

> Programmers waste enormous amounts of time thinking about, or worrying about, the speed of noncritical parts of their programs, and these attempts at efficiency actually have a strong negative impact when debugging and maintenance are considered.
> We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil. Yet we should not pass up our opportunities in that critical 3%. A good programmer will not be lulled into complacency by such reasoning, he will be wise to look carefully at the critical code; but only after that code has been identified.
> — Donald Knuth

---

在诸多程序中，我们能看到冗杂、混乱的逻辑和实现。是的，的确对一些当前的情况能跑，但是一碰就碎，一改就错。常见的例子：

* 绘图脚本，大量重复的函数调用、固定的参数——上千行代码只能画一张图，本以为只要修改一处结果改了20行；
* 同一个功能被多次反复实现，各自为政，直到某个时间点发生冲突，需要花大量时间查错；
* 乱七八糟的对象分类，一句话能说明白的东西非要绕个九曲十八弯，我调用你来你再调用我；
* 到处特事特办，每一个模块都极其“独立”，只能完成一项特定任务，换个对象别说买个新婚房，楼都要重新建;
* 菜市场一样的函数，啥都能卖，你也不知道你想买啥；
* 四处乱飞的全局变量，找到源头算你运气好；
* 超长的、无意义的注释。

> Any fool can write code that a computer can understand. Good programmers write code that humans can understand.

What is it that makes programs hard to work with?

* Programs that are hard to read are hard to modify.
* Programs that have duplicated logic are hard to modify.
* Programs that require additional behavior that requires you to change running code are hard to modify.
* Programs with complex conditional logic are hard to modify.

## Steps in refactoring

1. Building tests.
2. Changing the program in small steps, so it's easy to trace bugs. Follow the rhythm: test, small change, test, small change...
3. Never be afraid to rename things for clarity, especially internally[^2].

[^2]: 对于某些冥顽不化抱残守缺的码农，祝你好运。哦对了，Analysator至今的调用方式还是`import pytools`:sweat_smile:

Most refactorings reduce the amount of code. If you happen to have one which increases it, think twice.

Quoted from Don Roberts
> The first time you do something, you just do it. The second time you do something similar, you wince at the duplication, but you do the duplicate thing anyway. The third time you do something similar, you refactor.
> Three strikes and you refactor.

When do you need to refactor:

* when adding a function;
* when fixing a bug;
* when reviewing codes.

What you need to achieve with refactoring:

* To enable sharing of logic.
* To explain intention and implementation separately.
* To isolate change.
  * I use an object in two different places. I want to change the behavior in one of the two cases. If I change the object, I risk changing both. So I first make a subclass and refer to it in the case that is changing. Now I can modify the subclass without risking an inadvertent change to the other case.
* To encode conditional logic.
  * Polymorphism
  * Dispatch

### Special Attention to Classes and Objects

* 如果一项功能需要基于一个外来类的特性的判断，那很可能这个部分最好放在这个类自己的方法之中。
* 有时候判断分支可以用polymorphism来替代。或者in a Julian way, multiple-dispatch，基于变量类型的由编译器决定的函数调用，而不是程序员手写的分支。

### Changing Interfaces

If a refactoring changes a published interface, you have to retain both the old interface and the new one, at least until your users have had a chance to react to the change. Fortunately, this is not too awkward. You can usually arrange things so that the old interface still works. Try to do this so that the old interface calls the new interface. In this way when you change the name of a method, keep the old one, and just let it call the new one. Don't copy the method body—that leads you down the path to damnation by way of duplicated code. You should also use the deprecation facility in your programming language to mark the code as deprecated. That way your callers will know that something is up.

### When Shouldn't You Refactor?

There are times when you should not refactor at all. The principle example is when you should rewrite from scratch instead. There are times when the existing code is such a mess that although you could refactor it, it would be easier to start from the beginning. This decision is not an easy one to make, and there are no good guidelines for it.

Analysator就是一个很好的例子。

### Performance

Keep performance in mind, but as a general rule, cleaner code provides more space for optimization. Performance optimization often makes code harder to understand, but you need to do it to get the performance you need.

The interesting thing about performance is that if you analyze most programs, you find that they waste most of their time in a small fraction of the code. If you optimize all the code equally, you end up with 90 percent of the optimizations wasted, because you are optimizing code that isn't run much. The time spent making the program fast, the time lost because of lack of clarity, is all wasted time.

One live example quoted from the book:
> Our biggest improvement was to run the program in multiple threads on a multiprocessor machine. The system wasn't designed with threads in mind, but because it was so well factored, it took us only three days to run in multiple threads.

回想我为BATSRUS加多线程的经历，以及后续GPU代码的开发，真是感同身受。

### Bad Smells in Code

* Duplication
  * 我自己最常干这种事情是在写画图脚本的时候。当你一次性想画多张图的时候，你会很容易先画一个，然后复制粘贴改个变量名就弄出多个。然而事后你会发现这种做法是多么不利于代码的反复利用和阅读。引以为戒。
* Long method
* Large class
* Long parameter list
* Data clump: bunches of data that hang around together really ought to be made into their own object
* Switch statements: alert when you see the same switch statement scattered about a program in multiple places.
  * 我自己见过，也写过很多这样的例子。在实际操作中并非你一开始想象得那么简单：比如你最早写了一个绘图函数可以画等高线。后来你想扩展这个函数，可以改坐标单位。然而这个x、y轴的数据多处被用到，每一处用到的地方你都需要加一个有关单位的判断；然而按照原始的逻辑，把这些判断后的东西写在一起可能就比较奇怪。又比如Bart实现solid body的方法，非常的hacking，就是一个变量在原本算法逻辑中反复判断：这一步该不该打开，下一步该不该关上......最后的成品就是由无数个重复判断叠加在一起，对不对全看写的时候脑子清不清楚。
* Comments: many are misused as deodorant. It's surprising how often you look at thickly commented code and notice that the comments are there because the code is bad.
  * 最近的一个例子，Vlasiator中fix initial and boundary velocity block counts不一样的问题。之所以需要一大段注释，是因为代码本身逻辑混乱。
  * A good time to use a comment is when you don't know what to do, but not explain why you do poorly.

Here is a live example in Python 3.9 for bad smell in code:
  <iframe width="560" height="315" src="https://www.youtube.com/embed/LrtnLEkOwFE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Remember to always keep the code simple and self-descriptive, such that no extra explanatory comments are needed if possible.

## Mindset

Sometimes, refactoring is more about soft skills and decision making. I watched this interesting video

<iframe width="560" height="315" src="https://www.youtube.com/embed/CktRuMALe2A" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

while thinking more about my experience in real working environment. Select who to work with may be more important than the work itself.
