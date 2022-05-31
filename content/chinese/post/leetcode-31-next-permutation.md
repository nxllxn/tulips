---
title: "Next Permutation"
date: 2022-05-31T19:38:33+08:00
# post thumb
images:
- "images/leetcode/31-next-permutation.png"
# author
author: "郁金香啊"
# description
description: "Next Permutation"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---
整数数组的排列是将其成员排列成序列或线性顺序。

例如，对于 arr = `[1,2,3]`，其排列为`[1,2,3], [1,3,2], [3,1,2], [2,3 ,1]`。
整数数组的下一个排列是其整数的下一个字典序更大的排列。更正式地说，如果数组的所有排列都根据它们的字典顺序在一个容器中排序，那么该数组的下一个排列是在排序容器中跟随它的排列。

如果这种排列是不存在的，则必须将数组重新排列为尽可能低的顺序（即按升序排序）。

例如，arr = [1,2,3] 的下一个排列是 [1,3,2]。
类似地，arr = [2,3,1] 的下一个排列是 [3,1,2]。
而 arr = [3,2,1] 的下一个排列是 [1,2,3] 因为 [3,2,1] 没有更大的字典重排。
给定一个整数数组 nums，找到 nums 的下一个排列。

替换必须就位并且仅使用恒定的额外内存。

## 示例
**示例 1:**
```markdown
Input: nums = [1,2,3]
Output: [1,3,2]

**示例 2:**
```markdown
Input: nums = [3,2,1]
Output: [1,2,3]
```

**示例 3:**
```markdown
Input: nums = [1,1,5]
Output: [1,5,1]
```

**限制条件:**
* `1 <= nums.length <= 100`
* `0 <= nums[i] <= 100`

## 解决方案
给定任意一个排列，我们将其转换为十进制数字，要想找到比这个排列稍大的排列， 其实就是找到比这个数字稍大的数字。

不过这个稍大的数字只能通过交换数字中的数字顺序来得到。我们可以将整个交换过程分成两步。

第一步，我们想要得到一个比当前数字大的数字，我们可以通过将较大低位数字和较小的高位数字交换就好。比如数字123，我们通过交换2和3的位置，将处于个位的较大的数字3和处于十位的较小的数字2进行交换就得到了比它大的数字132。 此处132恰好是比数字123稍大的数字组合。

但是有时候我们得到的可能并不刚好是稍大的数字组合。比如132，我们找到比个位数小的百位数1然后进行交换，得到数字231，但是231并不是比132稍大的数字，我们期望的其实是213。

所以我们还需要进行第二步处理，那就是对发生交换的高位右边的序列进行处理。我们需要将他们重新排列成最小的值。比如这里的132到231，然后我们需要将高位2后面的31进行再次排列，得到一个最小值，那就是13。根据前面的交换逻辑我们可以知道，2之后的序列一定是降序的。我们只需要将这个序列翻转就可以得到最小值了。

```java
class Solution {
    //[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2],[3,2,1]
    //[1,2,3,4],[1,2,4,3],[1,3,2,4],[1,3,4,2],[1,4,2,3],[1,4,3,2],[2,1,3,4],[2,4,3,1]
    public void nextPermutation(int[] nums) {
        int index = nums.length - 2;
        
        while(index >= 0 && nums[index + 1] <= nums[index]) {
            index--;
        }
        
        if(index >= 0) {
            int j = nums.length - 1;
            while(nums[j] <= nums[index]) {
                j--;
            }
            swap(nums,index,j);
        }
        
        reverse(nums, index + 1);
    }
    
    private void swap(int[] nums, int i, int j) {
        nums[i] = nums[i] ^ nums[j];
        nums[j] = nums[i] ^ nums[j];
        nums[i] = nums[i] ^ nums[j];
    }
    
    private void reverse(int[] nums, int index) {
        int i = index, j = nums.length - 1;
        
        while(j > i) {
            swap(nums,i,j);
            i++;
            j--;
        }
    }
}
```