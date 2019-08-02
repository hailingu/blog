---
title: Longest Palindromic Substring
date: 2019-08-02 10:54:09
categories:
    - leetcode
tags: 
    - leetcode
    - array
    - hash table
    - palindromic
    - tow pointers
---

leetcode 的第 5 题，考察数组的数组的遍历。

    给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

<!-- more -->

直接的思路：假如当前字符的下标为 c，那么以 c 为中心，向两边扩展，找到最大的长度记为 l。把字符串中所有的字符都做为中心试一遍，把最长的结果输出就是最终的答案了。在这个直接的思路上，可以做额外的修改，以减少遍历的字符数，即 Manacher 算法。

假如当前的字符下标为 c，以 c 为中心，中心展开过后最大的回文长度为 2m 的话，那么在 c 的右侧的字符串长度即为 m。如果遍历到下一个字符 j，且 $j \in (c, c + m) $ 的时候，由于对称性的存在，j 关于 c 对称点 i 即为 c - (j - c) = 2c - j, $i \in (c - m, c)$。如果使用数组 p 保存每个点 c 的最大回文长度 2n 的话，那么计算中心点为 j 的时候的遍历起点，就可以从 min(p[i], c + m - j)，因为如果 p[i] > c + m - j 的话，意味着超出了以 c 为中心的判断边界，而边界外的情况是不知道的，需要继续使用中心展开。可以发现通过这个办法，把中心展开的部分点的起点，从 j + 1 移动到了 j + min(p[i], c + m - j)，减少了遍历字符的次数。

使用 Manacher 算法的时候，需要在原始字符串间插入占位字符，随便什么占位字符都可以，一般使用‘#’。使用占位符的原因，个人猜测是能让代码变得清晰简洁，否则就得对每个起始情况做额外的处理了。

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历，在中心扩展的过程中，已经访问过的节点没有再次被访问，而是使用了访问时间为 O(1) 的数组。
- 空间复杂度：O(n)，因为有一个数组 p 保存 n 个字符的回文半径。
- 执行结果：
    - 执行用时 : **10 ms**, 在所有 Java 提交中击败了 **94.88%** 的用户
    - 内存消耗 : **36.5 MB**, 在所有 Java 提交中击败了 **90.57%** 的用户

### Code 

```
public class LongestPalindrome {

    String longestPalindrome(String s) {
        if (s == null || 0 == s.length()) {
            return s;
        }

        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); ++i) {
            sb.append("#").append(s.charAt(i));
        }

        sb.append("#");
        int[] p = new int[sb.length()];
        p[0] = 1;
        int id = 0, mx = 0, hId = 0, hmx = 0;

        for (int i = 1; i < sb.length(); i++) {
            p[i] = mx > i ? Math.min(p[2 * id - i], mx - i) : 1;
            while (i + p[i] < sb.length() && i >= p[i] &&
                    sb.charAt(i + p[i]) == sb.charAt(i - p[i])) {
                p[i]++;
            }

            if (i + p[i] > mx) {
                mx = i + p[i];
                id = i;

                if (p[i] > p[hId]) {
                    hId = id;
                    hmx = mx;
                }
            }
        }

        int r = hmx - hId - 1;
        String tmp = sb.substring(hId - r, hId + r);
        StringBuilder ans = new StringBuilder();
        for (int i = 0; i < tmp.length(); ++ i) {
            if (tmp.charAt(i) != '#') {
                sb2.append(tmp.charAt(i));
            }
        }
        return ans.toString();
    }
}

```