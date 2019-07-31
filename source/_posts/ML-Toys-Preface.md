---
title: ML Toys Preface
date: 2019-07-30 18:41:09
categories:
    - machine learning
tags: 
    - machine learning
    - write machine learning algorithm by hands
    - ml toys
---

17 年年底的时候，组织过了一次公司内部的 machine learning 的学习和分享活动，当时积累下来不少有价值的内容，没有好好整理，趁着这次重整 blog 的机会，把这部分内容整理出来。幸运的是，自 17 年底来， machine learning 并没有再出现像 10 年以后的四五年内的爆发式的发展，所以这部分的内容不会显的特别过时。甚至，现在的 machine learning 有点凉凉的感觉，就如 40 年前一样。

<!-- more -->

这篇是一个开篇，并且这个系列 blog 不会刻意的总结 machine learning 所需要使用数学知识，会在具体实现某一个算法的时候，会总结出该算法所需要的必备的知识。之所以这个系列的 blog 的题目中含有 toy 这个词，是因为这样手动实现的算法是很难在工程上具有必须的效率和稳定性的，但是好处就是可以加深对算法的理解。在真实的环境中，也总是尽可能的调用开源的 machine learning 库的，令人安心的是，想要使用的算法，总是能找到对应的库、代码或者实现它的工具。

*纸上得来终觉浅，深知此事要躬行。*

这个系列 blog 的所有算法将会借助 Numpy 和 Python 3 来实现。

*脑子中一直有个想法，就是不用 numpy 实现算法，换句话说就是把 numpy 也再做一遍。*

那到底什么是 machine learning？说实话，这个定义和什么是 Artificial Intelligence 一样难给出，不同的人从不同的角度会给出不同的定义。这里贴一个大家普遍接受的定义：

    Tom Mitchell, Machine Learning：
        对于某类任务 T 和性能度量 M ，如果一个计算机程序 P 在 T 上以 M 衡量的性能随着经验 E 而自我完善，那么我们称这个计算机程序从经验 E 中学习。 

这种定义，看看就好，能理解更好。

现在大家普遍理解的任务 T 指的就是电影推荐、物体识别、物体分类这样的任务，当然还有高级一点的，如生成图像之类的任务，这些都可以是 T 的范畴，经验 E 常常指代是用于输入到计算机程序 P 中的 T 的历史数据 D，而 M 是用于评价程序 P 在任务 T 上表现的一些指标。

上面的话其实不是 Tom Mitchell 的原话，原话长这样：

    Tom Mitchell, Machine Learning：
        对于某类任务 T 和性能度量 P ，如果一个计算机程序在 T 上以 P 衡量的性能随着经验 E 而自我完善，那么我们称这个计算机程序从经验 E 中学习。 

知道了什么是 machine learning，就可以动手写第一个 machine learning 的程序了。

- *T: Task，任务*
- *D: Data，数据*
- *P: Program，程序*
- *M: Measurement，度量指标*
- *E: Experience，经验*