---
title: Reverse Integer
date: 2019-10-09 03:41:49
categories:
    - leetcode
mathjax: true
tags: 
    - leetcode
    - math
    - easy
---

leetcode 的第 7 题，考察链表的遍历和简单数学。

    给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

<!-- more -->

    在 Python 中这道题会特别的容易解决，因为不会 Python 中题目的输入取值范围 [$-2^{31}$, $2^{31}-1$] 不会成为写代码的障碍，可以考虑直接反转数字即可，即用到两个简单的步骤：

        1. 取数字的最后一位 x % 10
        2. 移除数字的最后一位 x / 10

    将每次取得的数字最后一位添加到要返回的结果中即可：ans = ans * 10 + x % 10

    要注意的是题目要求，在超过取值范围的时候，直接返回 0，这需要一些特殊处理。

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(1)，无额外存储
- 执行结果：
  - 执行用时 : **20 ms**, 在所有 Python 提交中击败了 **89.15%** 的用户
  - 内存消耗 : **11.6 MB**, 在所有 Python 提交中击败了 **35.44%** 的用户

### Code

    ```
    class Solution(object):
        def reverse(self, x):
            """
            :type x: int
            :rtype: int
            """

            sgn = 1 if x > 0 else -1
            x *= sgn

            ans = 0
            while x != 0:
                ans = ans * 10 + x % 10
                x = x / 10

            ans *= sgn
            return 0 if ans > 2147483647 or ans < -2147483648 else ans
    ```
