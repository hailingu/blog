---
title: Linux Memory Addressing
date: 2023-09-11 20:19:16
mathjax: true
tags:
  - linux
  - memory
  - addressing
---

在 Linux 中，程序经过编译后会生成 object file,这个文件是没办法直接运行的，还需要经过 link 和 load 阶段。在 load 阶段会通过系统调用创建**逻辑地址空间**，读取 link 后的 elf 文件加载到内存里，并设置其运行的地址空间为对应的**逻辑地址空间**。CPU 运行加载在内存中的程序，读取指令，生成 **逻辑地址** （或者叫做虚拟地址）。

## 段选择器和段寄存器

在 x86 中需要区分 3 种地址：

- 逻辑地址：一个逻辑地址的内容表示一个操作数或者指令，逻辑地址本身由**段选择符**和**段内偏移量**组成。比如下图的指令 4，表示为 [CS:EIP]，即 CS 表示段选择符，EIP 表示段内偏移量
- 线性地址：32bit 无符号数表达的连续地址
- 物理地址：CPU 往内存地址上发送的信号所表达的内存单元地址

它们的关系如图所示：

<!--more-->

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-1.png?raw=true)

程序里的逻辑地址，经过**Memory Management Unit(MMU)** 的硬件电路 **Segmentation Unit** 的处理，得到 **线性地址**。**线性地址** 由 **Paging Unit** 处理得到 **物理地址** 。CPU 拿到 **物理地址** 后，往内存地址总线上发送内存访问单元的物理地址信号，并由内存仲裁器决定，CPU 是否能立刻访问对应的内存地址。

一个逻辑地址由段标识符和偏移量组成

1. 段标识符：长度为 16bit，又被称为段选择器
2. 偏移量：长度为 32bit

段选择器，如下图所示：

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-4.png?raw=true)

Index 的部分长度为 13 bit，表示系统最可支持的最大分段数为 8192 个

CPU 通过提供段寄存器存储段选择器，实现了段选择器的快速存取。CPU 一共提供了 CS、SS、DS、ES、FS、GS 共 6 个段寄存器，其中 ES FS GS 为通用寄存器。

1. CS：存放代码段选择器
2. SS：存放程序的栈段选择器
3. DS：存放数据段选择器

一个段选择器的属性由一个 8 Byte 长度的段描述符表示，段描述符要么存储在 **全局描述符表(GDT)** 中，要么存储在 **局部描述符表(LDT)** 中。一般只存在 GDT，如果一个程序需要 GDT 之外的额外的段，那么程序可以将这些段存储在 LDT 中。GDT 和 LDT 的地址和长度存储在 gdtr 和 ldtr 控制寄存器中。

例如，gdtr 里存储的地址为 0x00001000，即 GDT 在内存里的地址是 0x00001000。同时，段选择符的 index 部分的值为 0x3e8（10 进制结果为 1000），那么段描述符在内存里的地址即为

0x000003e8 $\times$ 0x00000008 = 0x00001f40

0x00001000 $+$ 0x00001f40 = 0x00002f40

到目前为止，当拿到一个逻辑地址的时候，会通过下图所示的过程得到线性地址

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-5.png?raw=true)

可以发现这个流程还是挺长的。

一个典型的段描述符如下图所示：

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-3.png?raw=true)

在段选择器中有一个 RPL，在段描述符中有一个 DPL，还有一个 CPL。一般情况下 CPL = RPL，处理器在访问内存的时候，会比较 CPL 和 DPL，看权限是否符合要求，如果不符合要求就禁止访问，具体的规则在这里先忽略。

在 Linux 中广泛使用到数据段描述符（Data Segment Descriptor）和代码段描述符（Code Segment Descriptor），这两个可以存储在 GDT 或者 LDT 中。而任务状态段描述符（Task Status Segment Descriptor）只能存在于 GDT 中，这个段用来保存处理器寄存器的内容。LDTD（LDT 描述符）也只能存储在 GDT 中。

为了加速段描述符的读取，CPU 提供了 6 个不可编程的寄存器，用于存储段选择器对应的 8 Byte 段描述符。在每次段选择寄存器加载相应的段选择器的时候，段选择器所对应的段描述符也会加载到相应的不可编程寄存器中，这样获得线性地址流程被大大简化了，如下所示。

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-6.png?raw=true)

## Linux 中的段

在 Linux 逻辑地址中的段选择器 CS DS 对应的 Code Segment Descriptor 和 Data Segment Descriptor 中的起始地址总是 0x00000000，即直接使用线性地址，这样做的优势在于：

1. 方便把 Linux 系统移植到不同的架构中，如 aarch64， RISC V。
2. 内存管理更加简单，不同的进程使用同样的段寄存器

Linux 定义了 4 个宏，分别是 \_\_KERNEL_CS, \_\_KERNEL_DS, \_\_USER_CS 和 \_\_USER_DS，它们定义了内核代码描述符、内核数据段描述符，用户代码段描述符，用户数据段描述符在 GDT 的位置，可以在 linux/arch/x86/include/asm/segment.h 中找到相关的定义

```c
#define GDT_ENTRY_KERNEL_CS		12
#define GDT_ENTRY_KERNEL_DS		13
#define GDT_ENTRY_DEFAULT_USER_CS	14
#define GDT_ENTRY_DEFAULT_USER_DS	15


#define __KERNEL_CS			(GDT_ENTRY_KERNEL_CS*8)
#define __KERNEL_DS			(GDT_ENTRY_KERNEL_DS*8)
#define __USER_DS			(GDT_ENTRY_DEFAULT_USER_DS*8 + 3)
#define __USER_CS			(GDT_ENTRY_DEFAULT_USER_CS*8 + 3)
```

上面 (GDT_ENTRY_DEFAULT_USER_CS\*8 + 3) 的这个 +3 是为了设置前面提到过的
