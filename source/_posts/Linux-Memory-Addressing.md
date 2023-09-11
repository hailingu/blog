---
title: Linux Memory Addressing
date: 2023-09-11 20:19:16
tags:
  - linux
  - memory
  - addressing
---

# Linux Memory Addressing - I

在 x86 架构中，需要区分 3 种地址：

- 逻辑地址：x86 将程序分成多个段，每一个逻辑地址的内容表示一个操作数或者指令，逻辑地址本身由**段选择符**和**段内偏移量**组成。比如下图的指令 4，表示为 [CS:EIP]，即 CS 表示段选择符，EIP 表示段内偏移量
- 线性地址：32bit 无符号数表达的连续地址
- 物理地址：CPU 往内存地址上发送的信号所表达的内存单元地址

它们的关系如图所示：

<!--more-->

![lma-1](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-1.png?raw=true)

程序里的逻辑地址，经过**Memory Management Unit(MMU)** 的硬件电路 **Segmentation Unit** 的处理，得到 **线性地址**。**线性地址** 由 **Paging Unit** 处理得到 **物理地址** 。CPU 拿到 **物理地址** 后，往内存地址总线上发送内存访问单元的物理地址信号，并由内存仲裁器决定，CPU 是否能立刻访问对应的内存地址。
