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

简单做一下计算，在给定行数为 n 的情况下，第一行元素的下标分别为，设 q = 2 * n - 2：

    0, Idx[0] + 1 * q, Idx[1] + 1 * q, ...

第二行元素的下标为，设 l 为行号，step1 = q - 2 * l， step2 = q - step1：

    1， Idx[0] + step1, Idx[1] + step2, Idx[2] + step1, ...

直到最后一行：

    n, Idx[0] + q, Idx[1] + 1 * q, ...

*Idx[1] = Idx[0] + q 或者 Idx[1] = Idx[0] + step1，希望这个表述能清楚。*

有一些特殊情况直接返回字符串即可，即 n = 1 或者 n 大于字符串长度的时候。

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(1)，没有额外存储。
- 执行结果：
  - 执行用时 : **10 ms**, 在所有 Java 提交中击败了 **95.57%** 的用户
  - 内存消耗 : **40.4 MB**, 在所有 Java 提交中击败了 **83.84%** 的用户

### Code

    ```
    class Solution {
        public String convert(String s, int numRows) {
            if (numRows <= 1 || (s != null && numRows >= s.length())) {
                return s;
            }

            StringBuilder ans = new StringBuilder();
            int totalStep = 2 * numRows - 2;
            int nextStep = 0;
            for (int i = 0; i < numRows; ++i) {
                int j = i;
                nextStep = 2 * i;
                while (j < s.length()) {
                    ans.append(s.charAt(j));
                    if (nextStep == 0 || nextStep == totalStep) {
                        j += totalStep;
                    } else {
                        j += totalStep - nextStep;
                        nextStep = totalStep - nextStep;
                    }
                }
            }
            return ans.toString();
        }
    }
    ```
