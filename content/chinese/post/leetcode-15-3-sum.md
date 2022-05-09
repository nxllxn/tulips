---
title: "3Sum"
date: 2022-05-07T21:16:33+08:00
# post thumb
images:
- "images/leetcode/15-3-sum.png"
# author
author: "郁金香啊"
# description
description: "3Sum"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","困难难度"]
type: "regular"
draft: false
---

给定一组整数，返回所有可能的三元组`[nums[i], nums[j], nums[k]]`，其中`i != j, i != k, j != k`，使得三元组之和等于零。

此处要求返回的三元组不能包含重复的组合。

## 示例
**示例 1:**
```markdown
输入: nums = [-1,0,1,2,-1,-4]
输出: [[-1,-1,2],[-1,0,1]]
```

**示例 2:**
```markdown
输入: nums = []
输出: []
```

**示例 3:**
```markdown
输入: nums = [0]
输出: []
```

**限制条件:**
* `0 <= nums.length <= 3000`
* `-105 <= nums[i] <= 105`

## 解决方案
### 解决方案 1 - 排序 + 二分查找
此处题目要求找到三个数字，其和为零，我们可以先找两个数字，比如`m`和`n`，然后再找到第三个数字其等于`0 - m - n`。

我们此处可以先对数组进行排序，这样就可以使用二分查找。

我们通过控制数组索引下标范围来保证同一个数数字不被重复使用，比如数组长度是10的话，如果第一个数字索引是5，那么第二个数字就从第七个数字开始挑选。同理如果第二个数字索引是6，那么第三个数字应该从第八个数字开始挑选。

此处我们使用一个map进行去重，map的key使我们三个数字组成的字符串。
```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        
        Map<String,List<Integer>> map = new HashMap<>();
        
        for(int i = 0; i < nums.length && nums[i] <= 0; i++) {
            for(int j = nums.length - 1; j > i + 1 && nums[j] >= 0; j--) {
                int index = find(nums, i + 1, j - 1, -(nums[i] + nums[j]));
                
                if(index != -1) {
                    map.put(nums[i] + "" + nums[index] + "" + nums[j],List.of(nums[i],nums[index],nums[j]));
                }
            }
        }
        
        return new ArrayList(map.values());
    }
    
    int find(int[] nums, int start, int end, int num) {
        int low = start;
        int high = end + 1;
        int middle;
        while(low < high) {
            middle = low + (high - low) / 2;
            
            if(nums[middle] == num) {
                return middle;
            } else if(nums[middle] < num) {
                low = middle + 1;
            } else {
                high = middle;
            }
        }
        
        return -1;
    }
}
```
### 解决方案 2 - 排序 + 双指针
此处题目要求找到三个数字，其和为零，其实我们可以转换一下思路，就是我们可以找到两个数字，其和是第三个数字的相反数。

那么怎么快速的找到两个数字之和等于某一个特定的数字呢，答案就是将数组排序，然后从两端往中间进行遍历，如果遍历时两端的两个数字之和小于我们的目标值，我们将左边的游标往右移动，这样我们两个数字之和就会往大的方向发展，同理，如果遍历时两端的两个数字之和大于我们的目标值，我们将右边的游标左移动，这样我们两个数字之和就会往小的方向发展。

此处还需要注意，我们需要跳过相同的数字，以此来避免出现重复的三元组。

```java
class Solution {
    public List<List<Integer>> threeSum(int[] num) {
        Arrays.sort(num);
        List<List<Integer>> res = new LinkedList<>();

        for (int i = 0; i < num.length - 2; i++) {
            if (i == 0 || num[i] != num[i - 1]) {
                int lo = i + 1, hi = num.length - 1, sum = -num[i];
                while (lo < hi) {
                    if (num[lo] + num[hi] == sum) {
                        res.add(Arrays.asList(num[i], num[lo], num[hi]));
                        while (lo < hi && num[lo] == num[lo + 1]) lo++;
                        while (lo < hi && num[hi] == num[hi - 1]) hi--;
                        lo++;
                        hi--;
                    } else if (num[lo] + num[hi] < sum) lo++;
                    else hi--;
                }
            }
        }
        return res;
    }
}
```
