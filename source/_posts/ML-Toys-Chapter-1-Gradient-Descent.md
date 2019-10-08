---
title: Chapter 1 Gradient Descent
date: 2019-07-31 14:36:43
categories:
    - machine learning
mathjax: true
tags: 
    - machine learning
    - optimizer
    - write machine learning algorithm by hands
    - ml toys
---

### 所谓理论

梯度下降是数学优化里面的一个方法，数学优化的问题被定义成：

$$
\begin{split}
& \text{minimize } & f_0(x) \\
& \text{subject to } & f_i(x) \lt b_i,\ i = 1, \cdot \cdot \cdot, m.
\end{split}
$$

$\mathbf{x}$ 表示的是一个向量，对于所有满足约束条件的 $z$，如果 $f(\mathbf{z}) \gt f(\mathbf{x}^{\*})$，那么 $\mathbf{x}^{\*}$ 就是结果向量。

Gradient Descent，或者叫做梯度下降，就给出了求解 $\mathbf{x}^{\*}$ 的一个方法。当然使用梯度下降求解 $\mathbf{x}^{\*}$ 是有前提条件的，即 $f: \mathbf{R}^n \rightarrow R$上是[可微](https://zh.wikipedia.org/wiki/%E5%8F%AF%E5%BE%AE%E5%87%BD%E6%95%B0)的，并且去掉了其他的约束条件，所以它是求解无约束最优化的一种方法。

<!-- more -->

若函数 $f(\mathbf{x})$ 可微，并且在给定的定义域内是凸函数,不严格的来说，一定存在 $\mathbf{x}^{\*}$，因为可微条件的存在（函数在定义域空间内光滑，连续），使得可以采用在定义域内搜索的方法逐步找到最终的 $\mathbf{x}^{\*}$。既然是搜索的话，就需要一个起始点，记为 $\mathbf{s}$，在 machine learning 的领域中，有几种起始点：

- 零向量
- 使用高斯函数生成初始向量
- 随机初始化一个接近于零向量的向量

梯度下降即从点 $\mathbf{s}$ 开始，逐步的更新 $\mathbf{s}$ 的值，当 $ dist(\mathbf{s}, \mathbf{x}) \lt \varepsilon $ 时（$\varepsilon$ 是能容忍的误差），就可认为这样的 $\mathbf{s}$ 是想要的。从 $\mathbf{s}$ 到 $\mathbf{x}^{\*}$ 搜索的方向为 $-\nabla f(\mathbf{s})$，即会出现式子$\mathbf{s} = \mathbf{s} - \nabla f(\mathbf{s}) \cdot \xi $，其中 $\xi$ 就是搜索的步长。

综合起来，梯度下降算法写成：

$$
\begin{split}
& \text{1. 给定一个起始点 } & \mathbf{s} \in \mathbf{R}^n \\
& \text{2. 计算梯度} & -\nabla f(\mathbf{s}) \\
& \text{3. 更新 } & \mathbf{s} \text{, } \mathbf{s} =  \mathbf{s}-\nabla f(\mathbf{s}) \cdot \xi \\
& \text{4. 比较误差 } & dist(\mathbf{s}, \mathbf{x}) \lt \varepsilon \text{如果成立，转到步骤 5，否则转到步骤 2} \\
& \text{5. End}
\end{split}
$$

### Code

由于不同的 loss 对应着不同的计算梯度的方法，其中 ds 需要根据具体的 loss 求解出来。

    ```
        def gradient_descent(s, xi, ds):
            s = s - xi * ds
    ```
