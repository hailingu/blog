---
title: Add Two Numbers
date: 2019-07-30 15:23:46
categories:
    - leetcode
tags: 
    - leetcode
    - list
    - math
    - two pointers
---

leetcode 的第 2 题，考察数组的链表的遍历和简单数学。

    给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

<!-- more -->

题目非常的贴心，数组已经按照逆序存储了，也即数字 123 在这题里用链表的表示是 3 -> 2 -> 1。这题的解法是数字对应位数相加，需要注意的是下面的两个细节：

- 两个链表长度不一样如何办？
- 进位如何处理？

官方的示例：

    (2 -> 4 -> 3) 
          +        = (7 -> 0 -> 8)
    (5 -> 6 -> 4)

长度不等的情况：

    (2 -> 4 -> 3) 
          +        = (7 -> 4 -> 3)
    (5)

长度不等且进位的情况：

    (2 -> 4 -> 3) 
          +        = (1 -> 0 -> 4)
    (9 -> 5)

以及最终会进位的情况：

    (2 -> 4 -> 9) 
          +        = (1 -> 0 -> 0 -> 1)
    (9 -> 5)

当然还有一种长度相等且没有进位的情况，这种情况最简单，就不列了。如果结果的链表用 ans 表示，输入相加的两个链表用 l1 和 l2 表示，用 ans[i] 表示 ans 的第 i 个节点，carrier 表示进位，那么：

    temp_ans[i] = l1[i] + l2[i] + carrier
    carrier = temp_ans[i] / 10;
    ans[i] = temp_ans[i] % 10;

当然如果最后 carrier 不为 0 的话，那么最终的 ans 还需要再加上一个节点。

*需要把各种情况考虑周全，有时候会忘了相加到最后 carrier 不为 0 的时候还需要新增一个节点的情况。*

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(1)，额外的存储空间是常量，即 carrier 和一个虚拟的前驱节点。
- 执行结果：
    - 执行用时 : **5 ms**, 在所有 Java 提交中击败了 **96.35%** 的用户
    - 内存消耗 : **47.1 MB**, 在所有 Java 提交中击败了 **73.02%** 的用户

### Code

```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */

import java.util.List;
import java.util.ArrayList;

class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode ans = new ListNode(-1);
        ListNode i1 = l1, i2 = l2, ansIter = ans;

        int carrier = 0;
        while (i1 != null && i2 != null) {
            i1.val = i1.val + i2.val + carrier;
            carrier = i1.val / 10;
            i1.val = i1.val % 10;
            ansIter.next = i1;
            i1 = i1.next;
            i2 = i2.next;
            ansIter = ansIter.next;
        }
        
        if (i1 == null && i2 != null) {
            ansIter.next = i2;
            while (i2 != null && carrier > 0) {
                i2.val = i2.val + carrier;
                carrier = i2.val / 10;
                i2.val = i2.val % 10;
                ansIter.next = i2;
                i2 = i2.next;
                ansIter = ansIter.next;
            }
        }
        
        if (i1 != null && i2 == null) {
            ansIter.next = i1;
            while (i1 != null && carrier > 0) {
                i1.val = i1.val + carrier;
                carrier = i1.val / 10;
                i1.val = i1.val % 10;
                ansIter.next = i1;
                i1 = i1.next;
                ansIter = ansIter.next;  
            }
        }

        if (carrier > 0) {
            ansIter.next = new ListNode(1);
        }

        return ans.next;
    }
}
```