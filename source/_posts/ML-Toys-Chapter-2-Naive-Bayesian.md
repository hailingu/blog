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

### 所谓理论

朴素贝叶斯基于一个定理，和一个假设。定理即贝叶斯定理，假设呢就是各个特征条件独立，这个假设也是朴素贝叶斯名字里含有“朴素”俩字的原因。回顾一下贝叶斯定理：

<!-- more -->

$$
\begin{split}
& P(a|b)=\frac{P(b|a)P(a)}{P(b)} \\
& \text{subject to } P(b) \neq 0
\end{split}
$$

另一个特征的条件独立，意味着下面的式子成立：

$$
\begin{split}
P(x_1, x_2, \ldots , x_n | c_k) = P(x_1 | c_k)P(x_2 | c_k) \ldots P(x_n | c_k) = \prod\limits_{i = 1}^{n}P(x_i | c_k)
\end{split}
$$

这样结合贝叶斯定理，就可以计算出在给定特征 $x=(x_1, x_2, \ldots, x_n)$ 的情况下，就可以计算出这个 $x$ 属于类别 $c_k$ 的概率，即 $P(c_k | x_1, \ldots, x_n)$。

$$
\begin{split}
P(c_k | x_1, \ldots, x_n) & = & \frac{P(x_1, \ldots, x_n | c_k)P(c_k)}{P(x_1, \ldots, x_n)}
\end{split}
$$

仔细观察上面的式子，从数据集中，可以直接统计出 $P(x_1, \ldots, x_n)$、$P(x_1, \ldots, x_n | c_k)$、$P(c_k)$，这样计算出 $P(c_k | x_1, \ldots, x_n)$ 就很容易了。在实际工程中，上述的三个统计变量都存储在一张巨大的表中，所有的值都可以查表得到。

朴素贝叶斯典型的应用在 nlp 的语种分类，垃圾邮件分类，以及其他的一些分类中。
