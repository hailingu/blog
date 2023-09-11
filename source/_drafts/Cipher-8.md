---
title: Cipher 8
date: 2023-03-29 22:17:23
tags: cipher
mathjax: true
---

### 消息认证码

**完整性**，消息没有被篡改

**认证**，确认消息来自正确的发送者

**消息认证码**是确认消息完整性并认证的技术，称为 MAC

- 输入任意长度的消息
- 发送和接收者之间共享的密钥
- 消息的 1 比特发生变化，MAC 也会发生变化

<!--more-->

使用步骤

```text
1. 双方共享密钥
2. 发送者使用共享密钥对消息算出 MAC
3. 发送者将消息和 MAC 发送给接收者
4. 接收者共享密钥对接收到的消息算出 MAC
5. 接收者对比收到的 MAC 和自己计算的 MAC 是否一致
```

MAC 也会遇到密钥配送问题。

### 消息认证码的实现方式

1. 使用单向散列函数
2. 使用 AES 分组密码实现
3. Encrpyt-then-MAC，先用对称密码加密消息，再计算加密后结果的 MAC。接收者只需要计算出加密结果的 MAC 和收到的 MAC 是否一致就可以判断消息的来源

### HMAC

1. 密钥填充，在密钥的尾部填充 0
2. 填充后的密钥与 "0 0 1 1 0 1 1 0" 比特串计算 xor，结果填充到待计算消息的头部
3. 计算填充后的消息的散列值
4. 填充后的密钥与 "0 1 0 1 1 1 0 0" 比特串计算 xor，结果填充到散列值值的尾部
5. 再次计算填充后的散列值，输出结果

### 对消息认证码的攻击

1. 重放攻击，窃听者将正确的消息和 MAC 收到后，不断的向接收者发送同样的消息，接收者会把这些消息当作正确的消息进行处理。
2. 碰撞攻击，根据 MAC 值推测双方使用的密钥

### 消息认证码无法解决的问题

1. 第三方证明：发送者和接收者之间的消息无法交给第三方验证，第三方拿到正确的消息时候，无法判断消息来源于发送者或是接收者，因为两者都能发送正确的消息。
2. 防止否认：一条正确的消息发出后，发送者可以向第三方声明没有发出过这条消息，第三方也无法判断发送者是否说谎了，因为消息有可能是接收者自己发的

### 练习测试

1. 错误。不完全同意书上的解释。
   - 第一点：接收者解密后得到了看似合理的消息也不能判断消息是否真的来源于发送者。
   - 第二点：同意书上的解释，对于本身就是随机的字符串，双方用正确的密钥无法判断消息的真伪。
2. x o o x