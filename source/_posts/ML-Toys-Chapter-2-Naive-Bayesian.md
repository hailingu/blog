---
title: ML-Toys-Chapter-2-Naive-Bayesian
date: 2019-10-09 20:19:10
mathjax: true
tags: 
    - machine learning
    - nlp
    - naive bayesian
    - write machine learning algorithm by hands
    - ml toys
---

朴素贝叶斯基于一个定理，和一个假设。定理即贝叶斯定理，假设呢就是各个特征之间相互独立，这个假设也是朴素贝叶斯名字里含有“朴素”俩字的原因。回顾一下贝叶斯定理：

$$
\begin{split}
& P(a|b)=\frac{P(b|a)P(a)}{P(b)} \\
& P(b) \neq 0
\end{split}
$$
