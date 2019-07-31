---
title: Length of Longest Substring
date: 2019-07-30 16:56:03
categories:
    - leetcode
tags: 
    - leetcode
    - hash table
    - string
    - two pointers
    - slide window
---

leetcode 的第 3 题，考察数组的数组的遍历、滑动窗口和 hash 表。

    给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

<!-- more -->

题目要求的是最长字串的长度，并且要求子串里面的字符不重复，基于这个原因，需要使用 hash 表存放已经访问过的字符和其对应的下标 i + 1。如果持续的遍历字符串，直到遇到存在于 hash 表中的字符，此时的下标记为 j，那么这时候就需要计算字串的长度 len = j - map.get(s[j]) + 1，并且把 map 中的 s[j] 对应的 value 替换成 j。**这个时候需要一个技巧，在遍历的过程中需要记下滑动窗口的起始位置，为什么？因为不这么做的话，需要清理 map 中下标小于 j 的字符，而这么做了之后就不用了，例如：**

    a b c d e f g c q t  h  a
    0 1 2 3 4 5 6 7 8 9 10 11

如果不记起始位置，那么在遇到第 2 个 c（Idx[7]） 的时候，就必须从 map 中清理第一 c（Idx[2]） 及其左边的字符，如果不清理的话，遇到第二个 a（Idx[11]）的时候就会出错了，会把最大长度记成 11（Idx[11] - Idx[1] + 1），而正确答案是 9 （Idx[11] - Idx[3] + 1）。

如果记了起始位置，令其为 start，那么遇到 Idx[11] 开始计算长度的时候，需要计算的是 Idx[11] - max(start, map.get(s[11])) + 1，而这个时候的 start = 2（为什么？）。那什么时候需要修改 start？，如果遇到的重复字符在 [start, j) 之间的时候，就需要修改 start 的值了，start = map.get(s[j])。

*在第一次做的时候就没有记录起始位置，导致每次需要从 map 中弹出一些数据，降低了效率。*


### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(1)，因为最坏的情况下，整个字符串都是不同字符，但是实际上字符的数量本身是有限的，最大值就是个常量，在本题的测试用例中，字符数不超过 ascii 的范围。
- 执行结果：
    - 执行用时 : **6 ms**, 在所有 Java 提交中击败了 **99.45%%** 的用户
    - 内存消耗 : **37.6 MB**, 在所有 Java 提交中击败了 **91.98%** 的用户

### Code 

```
import java.util.HashMap;
import java.util.Map;

class LengthOfLongestSubstring {
    public int lengthOfLongestSubstring(String s) {
        int[] map = new int[128];
        
        int j = 0;
        int start = 0;
        int ans = 0;
        char c;

        while (j < s.length()) {
            c = s.charAt(j);
            if (map[c] > 0) {
                start = Math.max(map[c], start);
            }
            
            ans = Math.max(ans, j - start + 1);
            map[c] = j + 1;
            j ++;
        }
        return ans;
    }
}
```
*注：int 数组也可以是一种 hash 表*