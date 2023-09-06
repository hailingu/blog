---
title: Cipher 7
date: 2023-03-26 12:59:25
tags: ciper
mathjax: true
---

### 单向散列函数

**单向散列函数**，接收一串消息输入，输出一个*散列值*。

其特性是

- 无论消息多长都能计算出散列值
- 散列值的长度位 256 比特
- 计算速度快
- 消息不同散列值不同
- 抗碰撞性：改变了消息后，其散列值也**一定**会改变
- 单向性：从消息到散列值很容易，从散列值推出消息很难

<!--more-->

一些术语

- 原像：输入的消息
- 消息摘要/指纹：散列值
- 完整性：一致性

用途

- 判断软件是否被篡改：对比发布和的软件的散列值和原始站点公布的结果，不一致说明被篡改
- 基于口令的加密：将密码和 salt（伪随机数生成器生成的随机值）混合后计算得到的散列值，结果用于加密的密钥
- 消息认证码
- 数字签名
- 伪随机数生成器：可用单向散列函数构造
- 一次性口令

### 具体的单向散列函数

1. MD4、MD5：生成 128 位的散列值。这两个算的强碰撞性现在都已被击破
2. SHA-1、SHA-256、SHA-384、SHA-512
   - SHA-1 生成 160 位的散列值，其余的生成 256、384、512 位的散列值，剩余的这几个统称为 SHA-2
   - SHA-256 处理的消息上限为 $2^{64}$，SHA-384 和 SHA-512 的处理上限接近于 $2^{128}$
   - SHA-1 的强碰撞性已被攻破
3. RIPEMD：生成 160 为的散列值，也已被攻破
4. SHA-3（Keccak）
   - 可以生成任意长度的散列值
   - 对输入消息的长度没有限制

### Keccak

> 以 SHA-3 为例，b = 1600

计算过程

1. 将待加密的消息 M 填充，使其转换成比特后的长度为 r 的整数倍。填充的规则按正则表示为 {$1~0^*~1$}。填充后的结果记为 P。下图的消息 M 的长度为 6，填充的了 3 比特，所以 r 的长度为 3

![](https://github.com/hailingu/hailingu.github.io/raw/master/images/keccak-padding.png?raw=true)

2. 将消息 P 按 r 个比特进行分组，得到 $P_0, ...., P_{n-1}$，这里 $n = len(Pading(M))/r$

![](https://github.com/hailingu/hailingu.github.io/raw/master/images/keccak-padding-slice.png?raw=true)

3. 生成一个长度为 b 的全 0 比特串 $S$。$b=c+r$

4. 对 $P_0, ..., P_{n-1}$ 执行 $f(P_i \bigoplus r_i, c_i), i \in {0,..[n-1]}$，最终得到 $Z_0,...,Z_{n-1}$

![](https://github.com/hailingu/hailingu.github.io/raw/master/images/sponge-construction.png?raw=true)

5. 将 $Z_0,...,Z_{n-1}$ 按数据拼接起来就是 SHA-3 的结果。并且初始的 $S$ 在 absorbing 阶段完成之后，记为状态比特串 $A$

在步骤 3 中出现的 c 被称为*容量*，其意义在于混淆输出的结果，不至于泄露输入特征。

### Keccak-$f[b]$

执行 5 个步骤

1. $\theta$
   - 将 $A$ 按 25 比特长度分组，得到 64 个 $5 \times 5$ 的 2 维矩阵，记为 $5 \times 5 \times z$。这里的 $z=64$
   - 计算 $C[x, z]=A[x,0,z] \bigoplus A[x,1,z] \bigoplus A[x,2,z] \bigoplus A[x,3,z] \bigoplus A[x,4,z]$
   - 计算 $D[x, z]=C[(x-1)~mod~5, z] \bigoplus C[(x+1)~mod 5, (z-1)mod~w]$
   - 计算 $A'[x,y,z]=A[x,y,z] \bigoplus D[x,z]$
2. $\rho$，平移 $\theta$ 中的结果
3. $\pi$，旋转 $\rho$ 中的结果
4. $\chi$，将 $\pi$ 用 and 和 xor 再混淆
5. $\iota$
   - 创建一个长度为 64 的比特串 RC
   - $RC[2^j-1]=rc(j+7i),j \in [0,6]$ 这里的 i 表示第几个循环
   - $A'[0,0,z]=A'[0,0,z] \bigoplus RC[z]$
   - 这个 $rc$ 函数就是按一定的规则在比特串 "1 0 0 0 0 0 0 0" 内部做 xor 操作

上面的 5 个操作要循环 $12 + 2 \times 6 = 24$ 轮

更多细节建议参考 [wiki](https://en.wikipedia.org/wiki/SHA-3)，思想感觉源于 AES，把 2 维的 AES 变成了 3 维的。

**单向散列函数只能识别篡改，无法识别伪装**

练习测试

1. 因为完全不碰撞的单向散列函数，就会计算慢，长度也可能不是 256 比特。这和单向散列函数的性质相冲突。所以强抗碰撞性是更现实的解决方案。
2. Alice 没有判断保存在硬盘里的散列值是否和程序本身都被修改了
3. 不能，这种做法抗碰撞性太差，消息改变之后散列值也不一定会改变
4. x o o o x
