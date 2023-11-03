---
title: Linux Memory Addressing - I
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

Index 的部分长度为 13 bit，表示系统最可支持的最大分段数为 8192 个，在实际的 Linux 的 linux/include/asm-i386/segment.h 中，全局 GDT 里存放的段描述符数量为 32 个。

```c
/*
 * The GDT has 32 entries
 */
#define GDT_ENTRIES 32
```

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

Linux 定义了 4 个宏，分别是 \_\_KERNEL_CS, \_\_KERNEL_DS, \_\_USER_CS 和 \_\_USER_DS，它们定义了内核代码选择器、内核数据段选择器，用户代码段选择器，用户数据段选择器，可以在 linux/include/asm-i386/segment.h 中找到相关的定义

```c
#define GDT_ENTRY_DEFAULT_USER_CS 14
#define __USER_CS (GDT_ENTRY_DEFAULT_USER_CS * 8 + 3)

#define GDT_ENTRY_DEFAULT_USER_DS 15
#define __USER_DS (GDT_ENTRY_DEFAULT_USER_DS * 8 + 3)

#define GDT_ENTRY_KERNEL_CS  (GDT_ENTRY_KERNEL_BASE + 0)
#define __KERNEL_CS (GDT_ENTRY_KERNEL_CS * 8)

#define GDT_ENTRY_KERNEL_DS  (GDT_ENTRY_KERNEL_BASE + 1)
#define __KERNEL_DS (GDT_ENTRY_KERNEL_DS * 8)
```

结合前面提到的 16 位段选择器，其中低 3 位是设置 DPL 和 段是在 LDT 还是 GDT 中，那么 GDT_ENTRY_DEFAULT_USER_DS\*8 等价于将 14 << 3，即 14 左移 3 位，恰好填入了 16 位选择器的 index 的部分。而 +3 则恰好设置了段选择器的 RPL 为 3，如图所示

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-7.png?raw=true)

这些段选择器对应的段描述符在 Linux 设置如下表，可以将各个字段对应回前面的段描述符的图中。

| 段描述符    | Base       | G   | Limit   | S   | Type | DPL | D/B | P   |
| ----------- | ---------- | --- | ------- | --- | ---- | --- | --- | --- |
| user code   | 0x00000000 | 1   | 0xfffff | 1   | 10   | 3   | 1   | 1   |
| user data   | 0x00000000 | 1   | 0xfffff | 1   | 2    | 3   | 1   | 1   |
| kernel code | 0x00000000 | 1   | 0xfffff | 1   | 10   | 0   | 1   | 1   |
| kernel data | 0x00000000 | 1   | 0xfffff | 1   | 2    | 0   | 1   | 1   |

这里段描述符的 G 为 1，说明段的计数粒度为 4096B = $2^{12}$ B = 4 KB， Limit 的最大偏移量是 0xfffff = $2^4 \cdot 2^4 \cdot 2^4 \cdot 2^4 \cdot 2^4 = 2^{20}$，所以每个段的线性地址空间是 $2^{12} \cdot 2^{20} = 2^{32} = 4$ GB。另外一点，在 8086 CPU 有 20 根地址总线和 16 根数据总线，意味着可以 8086 CPU 可以寻址的空间是 1M = $ 2^{10} \times 2^{10} $ 大小的地址，一次可以处理的数据量是 16 bit。

在 Linux 中，如果遇到用户模式和内核模式切换，那么不需要考虑保存段选择器，只需要保存逻辑地址中的偏移量就好了。例如在用户模式切换到内核模式的时候，Linux 会保证 CS，DS，SS 中的段选择器从用户段选择器切换为内核段选择器。

## Linux 中的 GDT

Linux 会为每一个 CPU 创建单独的 GDT。多核 CPU 系统就有多个 GDT 表，而单核系统则只有一个 GDT 表。在 Linux 启动的时候，会加载 GDT 表进入 gdtr 寄存器中。前面提到过，Linux GDT 表的长度为 32，其中有 14 个段描述符是保留和未使用的，剩下的 18 个段描述符：

1. 1 个 Task Status Segment Descriptor(TSSD)，每个处理器的 TSSD 指向的 TSS 是不同的。所有的 TSS 都存储在 init_tss 数组中，第 n 个 cpu 对应的 GDT 的 TSSD 的 Base 指向的是 init_tss 中的第 n 项。TSSD 的 Limit 的总是 0xeb。

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-8.png?raw=true)

2. 1 个 LDT Descriptor ，被所有进程共享
3. 3 个 Thread Local Storage Descriptor，用于存储 Thread Local 的数据
4. 5 个 Plug and Play BIOS Descriptor，用于即插即用设备
5. 1 个特殊的 TSS 段描述符，用于处理 “Double Fault”
6. 1 个内核代码段描述符
7. 1 个内核数据段描述符
8. 1 个用户代码段描述符
9. 1 个用户数据段描述符
10. 3 个 Advanced Power Management 段描述符

LDT 在 Linux 不常被使用。有一个应用 LDT 的例子是 Wine。LDT 的内存存储在 default_ldt 数组中。

## 分页

要区分两个概念，**pages** 和 **page frames**。**线性地址** 的一段固定长度的连续地址区间称为 **pages** 。**物理内存** 的一段固定长度连续存储单元，称为 **page frames**。通常 pages 和 page frames 的大小是相同的，所以一个 page 的数据可以恰好装进一个 page frame 里。内存里存储这种 page 和 page frame 映射的数据结构叫做 **page table**。当前进程正在使用的 **page table** 在内存里的地址存储在 cr3 寄存器中，cr3 寄存器的长度是 32 位，其中低 12 位一定是 0。从 80386 开始 page 的大小为 **4KB**。

从 8088 处理器开始 32 位的线性地址被分成了 3 个部分，

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-9.png?raw=true)

分别是

- page directory
- page table
- offset

cr3 寄存器存储的是 page directory 的基础地址。例如一个 32 位的线性地址 0x18fe34ab, cr3 寄存器里存放的基础地址为 0x23aef000，线性地址转化为物理内存地址如下所示

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-10.png?raw=true)

如果只有一级 page table，那么需要存储 $2^{20}$ 个 4B 长度的地址，需要占用 4MB 的内存空间。

page directory 条目和 page table 条目的结构完全一致，每个条目占据 4B 大小。

- 20bit 用于存放下一级项目的物理地址
- Present flag：判断 page 是否存在于内存中。如果 Present Flag 没有被设置且需要访问 page，那么就会触发 Page Fault Exception。
- Dirty flag: 每一次**写操作**发生在 page 上时设置。
- Read/Write flag：page 的读写权限
- User/Supervisor flag：page/page table 的访问权限，如果设置为 0，那么段选择器里的 CPL 要小于 3，才能访问这个 page；
  如果是 1，那么谁都可以访问。
- PCD and PWT flags：设置硬件缓存处理 page 的方式
- Page Size flag：设置 2MB-4MB 大 page，设置后会少一级页表

![](https://github.com/hailingu/hailingu.github.io/blob/master/images/lma-11.png?raw=true)

### PAE

8086 有 20 根地址线可寻址 1MB 地址空间；80386 到 Pentium 有 32 根地址线可寻址 4GB 的地址空间。从 Pentium Pro 开始的 cpu 都有 36 根地址线，支持 64GB 的寻址。但要实现 32 位系统支持 64GB 的地址空间，需要通过 Intel 新引入的 PAE 技术。

在 64GB 的系统中，page frame 的个数变成了 $2^{24}$ 个，每个 page frame 的大小和之前的一样都是 4KB。因为 page frame 数量的改变，一个 page directory 所需要的 bit 数变成了 24bit 存放下一级的物理地址 + 12bit 的属性，一共 36bit，使用 8B 存储。这就导致了在 page table 大小不变的情况下，page table 的条目数从 1024 变成了 512。

同时新增了一级 PDPT 目录，该目录包含 4 个 8B 大小的条目。PDPT 存放在以 32B 对齐的整数被开始位置上，cr3 寄存器存储 PDPT 的基础地址，在 32 位系统上需要 27bit 存储地址。在 PAE 模式下，线性地址映射变成了

1. cr3 存储 PDPT 的地址
2. 31-30 位选择 4 个 PDPT 的一个
3. 29-21 位选择 512 个 page directory 中的一个
4. 20-12 位选择 512 个 page table 中的一个
5. 11-0 位选择 4KB 大小的 page frame 里偏移量

如果使用 2MB 的大页，那么线性地址映射变成

1. cr3 存储 PDPT 的地址
2. 31-30 位选择 4 个 PDPT 的一个
3. 29-21 位选择 512 个 page table 中的一个
4. 20-0 选择 2MB 大小的 page frame 里的偏移量

32 位系统使用 64GB 的内存，对普通程序来说，还是 4GB 的地址空间，只是内核程序员可以将 4GB 的地址空间映射到物理 64GB 内存上。这么干的一个好处就是 64GB 的内存可以跑更多的 32 位进程了。
