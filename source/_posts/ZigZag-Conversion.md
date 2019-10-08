---
title: ZigZag Conversion
date: 2019-08-02 15:17:35
categories:
    - leetcode
tags: 
    - leetcode
    - array
    - math
---

leetcode 的第 6 题，考察数组的数组的遍历。

    将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

    比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：
        
        L   C   I   R
        E T O E S I I G
        E   D   H   N

<!-- more -->

简单做一下计算，在给定行数为 numRows 的情况下，第一行元素的下标分别为，设 step = 2 * numRows - 2：

    Idx[0], Idx[0 + step], Idx[0 + step + step]...

第二行元素的下标为，设 l 为行号（此时 l = 1），step1 = step - 2 * l， step2 = step - step1：

    Idx[0 + l], Idx[0 + l + step1], Idx[0 + l + step1 + step2], Idx[0 + l + step + step1],  Idx[0 + l + 2*step]...

第三行元素的下标为，（此时 l = 2）：

    Idx[0 + l], Idx[0 + l + step1], Idx[0 + l + step1 + step2], Idx[0 + l + step + step1],  Idx[0 + l + step1 + step2 + step1 + step2]...

直到最后一行：

    Idx[0] + n - 1, Idx[1] = Idx[0] + step, Idx[2] = Idx[1] + 1 * step, ...

注意到 step = step1 + step2，即有 0 + l + step1 = 0 + l + step - 2 * l = step - l，所以在第 2 行到 n-2 行的下标可以修改为：

    Idx[0 + l], Idx[0 + step - l], Idx[0 + step], Idx[0 + 2*step - l] ...

有一些特殊情况直接返回字符串即可，即 n = 1 或者 n 大于字符串长度的时候。

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(1)，没有额外存储。
- 执行结果：
  - 执行用时 : **48 ms**, 在所有 Python 提交中击败了 **84.40%** 的用户
  - 内存消耗 : **11.7 MB**, 在所有 Python 提交中击败了 **46.04%** 的用户

### Code

    ```
    class Solution(object):
        def convert(self, s, numRows):
            """
            :type s: str
            :type numRows: int
            :rtype: str
            """
            l = len(s)
            if numRows <= 1 or (numRows >= l):
                return s

            ans = ''
            step = 2 * numRows - 2

            for i in range(numRows):
                j = 0
                while j + i < l:
                    ans += s[j + i]
                    if i != 0 and i != numRows - 1 and j + step - i < l:
                        ans += s[j + step - i]

                    j += step
            return ans  
    ```
