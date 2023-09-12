---
title: Linux Memory Addressing
date: 2023-09-11 20:19:16
tags:
  - linux
  - memory
  - addressing
---

在 Linux 中，程序经过编译后会生成 object file,这个文件是没办法直接运行的，还需要经过 link 和 load 阶段。在 load 阶段会通过系统调用创建**逻辑地址空间**，读取 link 后的 elf 文件加载到内存里，并设置其运行的地址空间为对应的**逻辑地址空间**。CPU 运行加载在内存中的程序，读取指令，生成 **逻辑地址** （或者叫做虚拟地址）。

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

段选择器，如下图所示，

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-4.png?raw=true)

![Alt text](lma-4.png)
CPU 通过提供段寄存器存储段选择器，实现了段选择器的快速存取。CPU 一共提供了 cs、ss、ds、es、fs、gs 共 6 个段寄存器，其中 es fs gs 为通用寄存器。

1. cs：存放代码段选择器
2. ss：存放程序的栈段选择器
3. ds：存放数据段选择器

一个段选择器的属性由一个 8 Byte 长度的段描述符表示，段描述符要么存储在 **全局描述符表(GDT)** 中，要么存储在 **局部描述符表(LDT)** 中。一般只存在 GDT，如果一个程序需要 GDT 之外的额外的段，那么程序可以将这些段存储在 LDT 中。GDT 和 LDT 的地址和长度分别存储在 gdtr 和 ldtr 寄存器中。
