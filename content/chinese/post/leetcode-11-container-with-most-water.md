---
title: "Container With Most Water"
date: 2022-04-26T21:05:33+08:00
# post thumb
images:
- "images/leetcode/11-container-with-most-water.png"
# author
author: "郁金香啊"
# description
description: "Container With Most Water"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定一个数组里面包含n个数字，这n个数字分别表示n个柱子的高度。

我们选定任意两根柱子，配合x轴，我们都能够组合成一个容器，找到所有柱子中任意两个组合所能组成的容器中，能够装水最多的那个，返回最大的容积。

注意，你不能倾斜容器。

# Container With Most Water
## 示例

**示例 1:**
```markdown
输入: height = [1,8,6,2,5,4,8,3,7]
输出: 49
解释: 这一组数字表示的柱子中，能够组成的最大容器是第二根柱子和最后一根柱子，整个容器的宽度是7，高度是7，最大容积是49
```

**Example 2:**
```markdown
输入: height = [1,1]
输出: 1
```

**限制条件:**
* `n == height.length`
* `2 <= n <= 105`
* `0 <= height[i] <= 104`

## 解决方案
### 解决方案 1 - 暴力破解
对于每一根柱子，我们可以遍历右边的所有柱子，然后计算出所有可能的组合对应的所有容积，然后就可以找到容积最大的那一个。示例代码如下。
```java
class Solution {
    public int _maxArea(int[] height) {    //超出时间限制
        int maxArea = 0;
        for(int index = 0;index < height.length;index ++){
            for(int position = index + 1;position < height.length;position ++){
                maxArea = Math.max((position - index) * Math.min(height[index],height[position]),maxArea);
            }
        }
        
        return maxArea;
    }
}
```

这个方法的时间复杂度是O(n*n)，这只是一个最容易想到的解决方案，在leetcode上，这个答案执行时间超时了。

### 解决方案 2 - 减少宽度增加高度
我们观察到此处计算整个容器的面积，取决于两个因素，一个是容器的高度，一个是容器的宽度。

高度取决于我们选择的两个柱子中高度较低的一个，而宽度则取决于我们左右两个柱子之间的间隔。

我们想一下，如果我们先选定最左和最右边两根柱子，此时我们得到一个容积，`min(H[0],H[n - 1]) * n`。

如果我们想要获得最大的容积，我们只能办法获得更高的高度和更宽的宽度，但是因为我们已经选取了最左最右两根柱子，所以宽度以及是我们能够达到的极限了，我们只能通过损失宽度来尝试找到更高的柱子来组装容器。

如果我从高度较低的那根柱子往中间移动，此时宽度会变窄，如果我们想要得到更大的容积，只能寄希望于我们移动过程中，遇到了更高的柱子，然后可以组装成更高的容器，得到更大的容积，所以如果柱子高度比我们移动的还要小，那么我们直接跳过，如果找到了比当前柱子更高的那个，我们就停下来对比一次容积，然后继续。

整体的处理逻辑如下
```java
class Solution {
    public int maxArea(int[] heights){
        int low = 0;
        int high = heights.length - 1;

        int maxArea = 0;
        while(low < high){
            maxArea = Math.max(maxArea,(high - low) * Math.min(heights[low],heights[high]));

            if(heights[low] < heights[high]){    //注意此处我们移动高度较小的那根柱子
                low ++;
            } else{
                high --;
            }
        }

        return maxArea;
    }
}
```
时间复杂度O(n)。