---
title: Find Median Sorted Arrays
date: 2019-07-31 10:33:21
categories:
    - leetcode
tags: 
    - leetcode
    - array
    - math
    - two pointers
    - hard
---

leetcode 的第 4 题，考察数组的遍历、双指针、二分查找和一点点数学。

    给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。

    你可以假设 nums1 和 nums2 不会同时为空。

<!-- more -->

因为要求复杂度为 O(log(m + n))，所以就不能用合并两个数组，然后直接输出最终结果的方法了，这样做的话，时间复杂度就为 O(m+n)，并且还有一个 O(m+n) 的空间复杂度，超过了题目的要求。

中位数是什么，中位数意味着，存在某个数 k，满足将 nums1 和 nums2 分别分成两部分，分割点的下标分别记为 i 和 j。这样的 i 和 j 满足：

    i + j = m - i + n - j + 1 = m + n - i - j + 1
    i = (m + n + 1) / 2 - j

这个式子让我们可以只在长度更长的数组中搜索（在短数组中搜索的话，这种方法还需要一个数组为空这种特殊情况）。*还需要考虑一些细节，即搜索到的 i 和 j 是不是满足条件的，以及一些边界情况。*

    nums1: 0, 1, 2, 3, ..., i-1, | i, i+1, i+2, ..., m
    nums2: 0, 1, 2, 3, ..., j-1, | j, j+1, j+2, ..., n

i 是搜索到的下标，所以肯定要满足 nums1[i] > nums2[j - 1]，同理 nums2[j] > nums[i - 1]。如果 nums1[i] < nums2[j - 1]，说明了 i 太小了，需要增大，同时由于 i = (m + n + 1) / 2 - j 式子的存在，i 的增大会自动的减小 j。

### 复杂度分析

- 时间复杂度：O(log(n))。因为只在两个数组中的一个进行二分查找。
- 空间复杂度：O(1)，没有额外的数据存储。
- 执行结果：
  - 执行用时 : **4 ms**, 在所有 Java 提交中击败了 **98.56%** 的用户
  - 内存消耗 : **51.9 MB**, 在所有 Java 提交中击败了 **72.01%** 的用户

### Code

    ```
    class Solution {
        public double findMedianSortedArrays(int[] A, int[] B) {
            int m = A.length;
            int n = B.length;

            if (m > n) {
                int[] temp = A;
                A = B;
                B = temp;
                int tmp = m;
                m = n;
                n = tmp;
            }

            int iMin = 0, iMax = m, halfLen = (m + n + 1) / 2;

            while (iMin <= iMax) {
                int i = (iMin + iMax) / 2;
                int j = halfLen - i;

                if (i < iMax && B[j - 1] > A[i]) {
                    iMin = i + 1;
                } else if (i > iMin && A[i - 1] > B[j]) {
                    iMax = i - 1;
                } else {
                    int maxLeft;
                    if (i == 0) {
                        maxLeft = B[j - 1];
                    } else if (j == 0) {
                        maxLeft = A[i - 1];
                    } else {
                        maxLeft = Math.max(A[i - 1], B[j - 1]);
                    }
                    if ((m + n) % 2 == 1) {
                        return maxLeft;
                    }

                    int minRight;
                    if (i == m) {
                        minRight = B[j];
                    } else if (j == n) {
                        minRight = A[i];
                    } else {
                        minRight = Math.min(B[j], A[i]);
                    }
                    return (maxLeft + minRight) / 2.0;
                }
            }
            return 0.0;
        }
    }
    ```