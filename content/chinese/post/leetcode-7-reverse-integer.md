---
title: "Reverse Integer"
date: 2022-04-23T18:57:33+08:00
# post thumb
images:
- "images/leetcode/7-reverse-integer.png"
# author
author: "郁金香啊"
# description
description: "Reverse Integer"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---
给定一个32位的整数，以十进制表示，返回将其所有数字位翻转之后得到的新的数值，如果由于翻转导致数字溢出，那么返回0。此处假定我们使用的系统不支持64位的整数。

# Reverse Integer
# 示例
**示例 1:**
```markdown
输入: x = 123
输出: 321
```

**示例 2:**
```markdown
输入: x = -123
输出: -321
```

**示例 3:**
```markdown
输入: x = 120
输出: 21
```

**限制条件:**
* `-2 ^ 31 <= x <= 2 ^ 31 - 1`

## 解决方案
### 解决方案 1
让我们再次回顾题目，整个翻转过程其实并不复杂，但是唯一需要注意的就是，我们可能发生溢出。

比如假定我们使用的不是32位整数，而是八位整数，其范围在[-128 ~ 127]之间。那么当我们尝试对这个范围内的一些整数进行翻转时可能会溢出，比如127，翻转之后是721，显然它已经远远超过了8位整数所能表示的范围。

那么我们如何检测是否发生了溢出呢？让我们看一下整个翻转流程
```markdown
         x        y
1)       127      0
2)       12       7 (0 * 10 + 7)
3)       1        72  (7 * 10 + 2)
4)       0        721  (72 * 10 + 1)  72 * 10 此处其实就已经发生了溢出
```

在发生溢出的时候，我们的数字会被截断，那么意味着，72 * 10的结果将不再是720了，很棒，聪明的你一定已经发现怎么处理了，如果一个数字乘以10再除以10不等于原来的数字，那么我们就知道乘以10的计算会发生溢出了。

好了，让我们看下代码实现。

```java
class Solution {
    public int reverse(int x) {
        int sign = x < 0 ? -1 : 1;    //先取出符号
        x = x * sign;    //去掉x的符号
        
        int reverseValue = 0;
        int oldValue;
        while(x > 0){
            oldValue = reverseValue;
            
            reverseValue *= 10;
            
            if(reverseValue / 10 != oldValue){    //在这里检测是否发生了溢出。
                return 0;
            }
            
            reverseValue += x % 10;
            x /= 10;
        }
        
        return reverseValue * sign;    //加上符号
    }
}
```