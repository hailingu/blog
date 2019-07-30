---
title: Two Sum
date: 2019-07-30 14:40:22
categories:
    - leetcode
tags: 
    - leetcode
    - array
    - hash table
---

leetcode 的第 1 题，考察数组的数组的遍历和 hash 表的使用。

    给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

<!-- more -->

也就是要找出两个下标 i 和 j，满足 i < j，且 nums[i] + nums[j] = target，也即 nums[i] = target - nums[j]。后面这个式子说明了，在遍历数组的过程中，对于当前的值 nums[i]，要在 hash 表中查找是否存在 target - nums[j]，如果存在，那么就可以得到对应的 i 和 j 了；如果不存在，那么就需要在 hash 表中存入 target - nums[i] 和 i 以供往后的遍历查找使用。

### 复杂度分析

- 时间复杂度：O(n)，因为一次遍历。
- 空间复杂度：O(n)，因为最坏的情况下，即最后两个元素才是最终的答案， hash 表中要存储 n - 1 个元素。
- 执行结果：
    - 执行用时 : **3 ms**, 在所有 Java 提交中击败了 **99.05%** 的用户
    - 内存消耗 : **37.1 MB**, 在所有 Java 提交中击败了 **91.93%** 的用户

### Code 

```
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        int[] ans = new int[2];

        for (int i = 0; i < nums.length; ++i) {
            if (map.containsKey(nums[i])) {
                ans[0] = map.get(nums[i]);
                ans[1] = i;
                break;
            } else {
                map.put(target - nums[i], i);
            }
        }
        return ans;
    }
}
```

注: *使用 break 的原因是为了尽可能的保证这个函数只能通过最后一行返回结果。*