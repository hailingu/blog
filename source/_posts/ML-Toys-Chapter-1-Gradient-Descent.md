---
title: Chapter 1 Gradient Descent
date: 2019-07-31 14:36:43
categories:
    - machine learning
tags: 
    - machine learning
    - optimizer
    - write machine learning algorithm by hands
    - ml toys
---

梯度下降是数学优化里面的一个方法，数学优化的问题被定义成：

$$
\begin{split}
& \text{minimize } & f_0(x) \\
& \text{subject to } & f_i(x) \lt b_i,\ i = 1, \cdot \cdot \cdot, m.
\end{split}
$$

$x$ 表示的是一个向量，对于所有满足约束条件的 $z$，如果 $f(z) \gt f(x^{\*})$，那么 $x^{\*}$ 就是最后的结果向量。

<!-- more -->

Gradient Descent，或者叫做梯度下降，就给出了求解 $x^{\*}$ 的一个方法。当然有一个前提条件，即 $x^{\*}$ 这个向量在当前的问题设定下是存在的。在实际的使用中，我们常常不会去检查 $x^{\*}$ 是否在要求解的问题。 