---
title: Cipher 2
date: 2023-03-19 10:06:14
tags: Cipher
---

### 凯撒密码（平移密码）

凯撒密码就是将字母表里的字母按顺序平移一定的位数。例如 a 平移 3 位就变成了 d，以此类推可以得到下面这张码表

```text
a b c d e f g h i j k l m n o p q r s t u v w x y z
d e f g h i j k l m n o p q r s t u v w x y z a b c
```

<!--more-->

如是使用上面的码表加密 “hello world” 的结果是 “khoor zruog”。即

```text
h -> k
e -> h
l -> o
l -> o
o -> r
w -> z
o -> r
r -> u
l -> o
d -> g
```

在这个例子中，密钥是：英文字母表按顺序平移 3 位。接收者需要知道这个密钥就可以解密。

    密钥：加密过程中需要使用的一段符号，文字或者数字

因为字母表只有 26 个字母，所以可以将 0 到 25 这 26 个数字全部尝试一遍，即可破解凯撒密码了。这种破解方式称之为**暴力破解**（见后面的练习测试 1）。

### 简单替换密码

简单替换密码是生成一个明文字母表和密文字母表的对应关系。凯撒密码也是一种简单替换密码，它生成对应关系的方式是通过*平移字母*这个算法。

如书上给出的一个码表

```text
a b c d e f g h i j k l m n o p q r s t u v w x y z
w y h f x u m t j v s g e n b r d z l q a p c o k i
```

这个时候加密 “hello world” 的结果是 “teggb cbzgf”

```text
h -> t
e -> x
l -> g
l -> g
o -> b
w -> c
o -> b
r -> z
l -> g
d -> f
```

这里的密钥是：上面的那张码表。接收者需要知道这个码表，就可以解密信息。

简单替换密码的密钥空间为 $26 \times 25 \times ... \times 1 = 4032914561126605635584000000 $。简单替换密码想通过*遍历密钥*暴力破解，几乎不可能。破解简单替换密码，可以通过**频率分析**。该方法利用明文中出现的字母频率和密文中出现的字母频率是一致的。

### Enigma

Enigma 是一台可以自动生成简单替换密码码表的机器，或者是一个生成简单替换密码的算法。

加密过程

1. 根据每日密码本设定 Enigma 机器
2. 选择通信密码，例如 “abc”，并用 Enigma 加密后得到一个字符串 “cde”
3. 根据通信密码的字符串 “abc”，来设定 Enigma 的转子位置
4. 加密需要发送的明文消息，例如 “helloworld”，得到 “tgesastsgw”
5. 将 2 和 5 中的信息拼接后 “cdetgesastsgw” 发送出去

解密过程

1. 根据每日密码本设定 Enigma 机器
2. 将收到信息的前 3 位取出，即 “cde”，输入 Engima 得到 “abc”
3. 根据 “abc” 重新设置 Enigma 机器
4. 将 “tgesastsgw” 输入 Enigma 得到 “helloworld”

这里的密钥有两个

1. 加密通信密码的密钥，每日密码本
2. 加密通信消息的密钥，通信密钥

Enigm 的弱点

1. 每日密码本，没有这个做不了加密和解密，被人拿到这个破译密码的难度大大降低
2. 通信密码是人为确定，人很容易选择有“意义”的信息作为通信密码，或者简单设定通信密码，降低了破译难度
3. 拿到 Enigma 机器后，通过分析结构，结合拿到的大量密文，可以大大降低解谜的问题空间

### 思考

上面的 3 个加密方式，一个共同的特点是：加密算法和密钥确定的时候，密码就被破译了。所以加密解密方面努力的方向是将加密算法和密钥进行分开，希望加密算法重复使用，但是密钥可以经常更换。这就带来了另一个问题，如何生成和管理不同的密钥。

### 练习测试

1. 使用程序进行 “PELCGBTENCUL” 的暴力破解（前提是这段密文使用凯撒密码加密）。

```python
cipher_text = 'PELCGBTENCUL'
for i in range(0, 26):
    decipher = ''   # 解密结果
    translation = 0 # 平移位数
    for c in cipher_text.lower():
        decipher_c = (ord(c) - ord('a') + i) % 26 + ord('a')
        decipher = decipher + chr(decipher_c)
    translation = ord(decipher[0]) - ord(cipher_text.lower()[0])
    if translation < 0:
        translation += 26
    print(translation, decipher)
```

跑出来的结果如下

```text
0 pelcgbtencul
1 qfmdhcufodvm
2 rgneidvgpewn
3 shofjewhqfxo
4 tipgkfxirgyp
5 ujqhlgyjshzq
6 vkrimhzktiar
7 wlsjnialujbs
8 xmtkojbmvkct
9 ynulpkcnwldu
10 zovmqldoxmev
11 apwnrmepynfw
12 bqxosnfqzogx
13 cryptography
14 dszquphsbqiz
15 etarvqitcrja
16 fubswrjudskb
17 gvctxskvetlc
18 hwduytlwfumd
19 ixevzumxgvne
20 jyfwavnyhwof
21 kzgxbwozixpg
22 lahycxpajyqh
23 mbizdyqbkzri
24 ncjaezrclasj
25 odkbfasdmbtk

Process finished with exit code 0

```

可以看到 **13 cryptography**，这个就是我们想要的结果，加密密钥即为平移 13 位。

2. 不正确，对破译难度没有任何影响。 书上给出的解释的前提是破译者知道字母不会被相同的字母替换，但是这个前提是不会预先被破译者知道的。
