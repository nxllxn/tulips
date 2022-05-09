---
title: "3Sum Closest"
date: 2022-05-09T20:02:33+08:00
# post thumb
images:
- "images/leetcode/16-3-sum-closest.png"
# author
author: "郁金香啊"
# description
description: "3Sum Closest"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","困难难度"]
type: "regular"
draft: false
---
给定一组整数以及一个目标数，找到任意三个数字使得这三个数字之和最接近目标数。返回这三个数字之和。

你可以假定每一个输入仅仅包含一个解。

## 示例
**示例 1:**
```markdown
输入: nums = [-1,2,1,-4], target = 1
输出: 2
解释: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

```markdown
**示例 2:**
输入: nums = [0,0,0], target = 1
输出: 0
```

**限制条件:**
* `3 <= nums.length <= 1000`
* `-1000 <= nums[i] <= 1000`
* `-104 <= target <= 104`

## 解决方案
### 解决方案 1 - 排序 & 双指针
根据[上一道题目](../../post/leetcode-15-3-sum/)的解决方案，我们很容易得到这道题目的解决方案，思路基本相似，唯一需要调整的就是记录下最小的差别以及得到最小差别时的总和。

```java
class Solution {
    public int threeSumClosest(int[] num, int target) {
        Arrays.sort(num);

        int minDifference = Integer.MAX_VALUE;
        int sum = 0;

        for (int i = 0; i < num.length; i++) {
            int lo = i + 1, hi = num.length - 1;
            while (lo < hi) {
                if (Math.abs(num[i] + num[lo] + num[hi] - target) < minDifference) {
                    minDifference = Math.abs(num[i] + num[lo] + num[hi] - target);
                    sum = num[i] + num[lo] + num[hi];
                }

                if (num[i] + num[lo] + num[hi] > target) {
                    hi--;
                } else {
                    lo++;
                }
            }
        }

        return sum;
    }
}
```