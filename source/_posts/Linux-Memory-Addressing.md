---
title: Linux Memory Addressing - I
date: 2023-09-11 20:19:16
tags:
  - linux
  - memory
  - addressing
---

在 Linux 中，ELF 典型的文件构成如下图所示

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-2.png?raw=true)

ELF 文件被分成了多个段，每个段由**逻辑地址**组成。

在 x86 中需要区分 3 种地址：

- 逻辑地址：一个逻辑地址的内容表示一个操作数或者指令，逻辑地址本身由**段选择符**和**段内偏移量**组成。比如下图的指令 4，表示为 [CS:EIP]，即 CS 表示段选择符，EIP 表示段内偏移量
- 线性地址：32bit 无符号数表达的连续地址
- 物理地址：CPU 往内存地址上发送的信号所表达的内存单元地址

它们的关系如图所示：

<!--more-->

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-1.png?raw=true)

程序里的逻辑地址，经过**Memory Management Unit(MMU)** 的硬件电路 **Segmentation Unit** 的处理，得到 **线性地址**。**线性地址** 由 **Paging Unit** 处理得到 **物理地址** 。CPU 拿到 **物理地址** 后，往内存地址总线上发送内存访问单元的物理地址信号，并由内存仲裁器决定，CPU 是否能立刻访问对应的内存地址。

## 段选择器和段寄存器

一个逻辑地址由段标识符和偏移量组成：

1. 段标识符：长度为 16bit，又被称为段选择器
2. 偏移量：长度为 32bit

CPU 通过提供段寄存器存储段选择器，实现了段选择器的快速存取。CPU 一共提供了 cs、ss、ds、es、fs、gs 共 6 个段寄存器：

1. cs：存放代码段选择器，其中使用 2bit 为表示 CPU 的运行模式，0 表示内核模式，3 表示用户模式
2. ss：存放程序的栈段选择器
3. ds：存放数据段选择器
