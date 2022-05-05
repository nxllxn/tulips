---
title: "Longest Common Prefix"
date: 2022-05-04T13:10:33+08:00
# post thumb
images:
- "images/leetcode/14-longest-common-prefix.png"
# author
author: "郁金香啊"
# description
description: "Longest Common Prefix"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","简单难度"]
type: "regular"
draft: false
---

这道题是简单难度的题目，本来我准备跳过这道题目的，但是看了另一篇博客，里面给出了很多种可选的解决方案，每一种解决方案体现的思路还是很不错的。

实现一个函数来计算一组字符串中的公共最长前缀字串。如果没有找到最长公共字串，那么直接返回空串。

## 示例
*示例 1:*
```markdown
输入: strs = ["flower","flow","flight"]
输出: "fl"
```

*示例 2:*
```markdown
输入: strs = ["dog","racecar","car"]
输出: ""
解释: There is no common prefix among the input strings.
```

*限制:*
* `1 <= strs.length <= 200`
* `0 <= strs[i].length <= 200`
* `strs[i]`中仅仅包含英文字母

## 解决方案
### 解决方案 1 - 纵向比较
我们将这一组字符串的字符序列一行行排列起来，然后一列一列进行比较，如果某一列字符不匹配，或者某一行字符串用完了，那么这一列之前的字串就是我们要找到最长字串。

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs == null || strs.length == 0) {
            return "";
        }
        
        for(int column = 0;column < strs[0].length();column++) {
            char c = strs[0].charAt(column);
            for(int row = 1; row < strs.length; row++) {
                if(strs[row].length() == column || strs[row].charAt(column) != c) {
                    return strs[0].substring(0, column);
                }
            }
        }
        
        return strs[0];
    }
}
```

可以看出，如果所有的字符串均相同，此时我们的结果是最差的，时间复杂度达到`O(m * n)`，此处m是字符串的数量，n是字符串的长度。空间复杂度此处为1。

### 解决方案 2 - 水平比较
我们先考虑只有两个字符串的场景，我们很容易编写代码实现得到最长公共前缀，最多花费`Math.min(s1.length(), s2.length())`。

我们可以看出，单次计算的最大时间复杂度取决于字符串中较短的那个，然后基于一个事实，绝大多数字符串最长公共前缀大概率比两者都短，所以如果我们使用第一次计算的结果再进行下一步计算的话，理论上我们的时间复杂度会越来越小。

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs == null || strs.length == 0) {
            return "";
        }
        
        String prefix = strs[0];
        
        for(int row = 1; row < strs.length; row++) {
            int minLength = Math.min(prefix.length(), strs[row].length());
            
            int column = 0;
            while (column < minLength && prefix.charAt(column) == strs[row].charAt(column)) {
                column++;
            }
            
            prefix = prefix.substring(0, column);
        }
        
        return prefix;
    }
}
```

最坏情况下，也就是所有字符串是一样的，此时时间复杂度和解法一一致。正常情况下，在最短字符串之前的所有字符串需要比较更多的字符。比如字符串数组`['abcdefg','abcdefg','']`，解法一，我们只需要三次比较就结束了，但是解法二前两个字符串进行了大量的比较。

### 解决方案 3 - 递归、归并
我们可以换一个思路，假定我们有四个字符串，最终的最长公共前缀其实就是前两个字符串和后两个字符串的最长公共前缀的最长公共前缀，所以我们其实可以用递归的方式来解决这个问题，类似归并的操作。

```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if(strs == null || strs.length == 0) {
            return "";
        }

        return longestCommonPrefix(strs, 0, strs.length - 1);
    }

    public String longestCommonPrefix(String[] strs, int low, int high) {
        if(low == high) {
            return strs[low];
        }
        
        int middle = low + (high - low) / 2;
        
        return commonPrefix(
            longestCommonPrefix(strs, low, middle),
            longestCommonPrefix(strs, middle + 1, high)
        );
    }
    
    private String commonPrefix(String s1, String s2) {
        int minLength = Math.min(s1.length(), s2.length());
        
        int index = 0;
        while(index < minLength && s1.charAt(index) == s2.charAt(index)) {
            index++;
        }
        
        return s1.substring(0, index);
    }
}
```