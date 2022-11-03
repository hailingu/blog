---
title: Palindrome Number
date: 2019-10-10 15:41:43
categories:
    - leetcode
tags: 
    - leetcode
    - math
    - palindromic
    - easy
---

leetcode 的第 9 题，考察简单数学。

    判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

<!-- more -->

题目的例子里给出了负数都不是回文数，剩下的就只需要考虑正整数了。回文数的特点就是从左向右读和从右向左读是一样的，即数字进行反转了之后会和原来的数相等的数即是回文数。而整数反转，在第 7 题已经做过了。

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(1)，无额外存储
- 执行结果：
  - 执行用时 : **68 ms**, 在所有 Python 提交中击败了 **54.33%** 的用户
  - 内存消耗 : **11.7 MB**, 在所有 Python 提交中击败了 **29.21%** 的用户

### Code

    ```
    class Solution(object):
        def isPalindrome(self, x):
            """
            :type x: int
            :rtype: bool
            """
            
            if x < 0:
                return False

            test = 0
            origin = x
            while x > 0:
                test = test * 10 + x % 10
                x = x / 10

            return test == origin
    ```
