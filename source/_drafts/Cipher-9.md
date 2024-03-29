---
title: Cipher 9
date: 2023-04-01 13:38:38
tags: cipher
mathjax: true
---

### 数字签名

在消息认证码中，因为发送者和接收者使用的是同样的密钥，所以无法解决否认的问题。那么在数字签名的方案中，发送者和接收者将会使用不同的密钥，这样正确解码的消息就能判断消息的来源了。

- 生成消息签名，发送者对要发送的消息生成消息签名
- 验证数字签名，验证消息是否真的来自于消息的发送者

<!--more-->

数字签名使用公钥密码系统可以解决，发送者持有私钥，并用私钥对消息生成签名，接收者通过公钥验证消息的准确性。

签名过程

$$
signature=plaintext^{D}~mod~N
$$

验证过程

$$
plaintext=signature^{E}~mod~N
$$

### 数字签名的方法

#### 直接对消息签名

```text
1. Alice 用私钥对消息加密，把加密后的消息和消息明文发给 Bob
2. Alice 将公钥发给 Bob，Bob 用公钥解密收到的加密消息
3. 如果 Bob 解密后的消息和收到的明文消息一致，那么验证成功
```

#### 对消息的散列值签名

```text
1. Alice 用单向散列函数，计算消息的散列值
2. Alice 用私钥加密消息的散列值，然后将加密后的散列值和消息的明文发送给 Bob
3. Bob 使用同样的散列函数，计算消息的散列值
4. Bob 使用公钥解密收到的加密散列值
5. 对比 Bob 自己计算的散列值和收到的散列值，如果一致，那么验证成功
```

### 数字签名的应用

- 安全信息公告
- 对软件签名
- 公钥证书
- SSL/TLS

### 其他数字签名算法

- ElGamal
- DSA
- ECDSA
- Rabin

### 练习测试

1

- 私钥
- 公钥

2. x o x
