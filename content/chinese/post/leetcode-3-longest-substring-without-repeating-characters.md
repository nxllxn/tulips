---
title: "Longest Substring Without Repeating Characters"
date: 2022-04-20T09:13:33+08:00
# post thumb
images:
- "images/leetcode/3-longest-substring-without-repeating-characters.png"
#author
author: "郁金香啊"
# description
description: "Longest Substring Without Repeating Characters"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定一个字符串，找到它里面不包含重复字符的最长的字串。

# Longest Substring Without Repeating Characters
## 示例

**示例 1:**

```markdown
输入: s = "abcabcbb"
输出: 3
解释: 满足条件的最长字串是"abc"，字符串长度是3
```

**示例 2:**

```markdown
输入: s = "bbbbb"
输出: 1
解释: 满足条件的最长字串是"b"，长度是1
```

**示例 3:**

```markdown
输入: s = "pwwkew"
输出: 3
解释: 满足条件的最长字串是"wke"，长度是3
```

**Constraints:**
* `0 <= s.length <= 5 * 10000`
* 字符串可能包含英文字母，数字，特殊符号以及空格

## 解决方案
### 解决方案 1 - BitSet

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        java.util.BitSet bitSet = new java.util.BitSet(128);

        int maxLength = 0;
        int currentLength = 0;
        int length = s.length();
        char c;
        for (int index = 0; index < length; index++) {
            currentLength = 0;
            bitSet.clear();
            for (int position = index; position < length; position++) {
                c = s.charAt(position);
                if (bitSet.get(c)) {
                    break;
                }

                bitSet.set(c);
                currentLength += 1;
            }

            maxLength = Math.max(maxLength, currentLength);
        }

        return maxLength;
    }
}
```

此处使用`BitSet`来记录一个字符是否出现过，因为根据题目描述，字符串中包含的是ASCII码。所以长度128的BitSet就可以满足我们的需求。空间复杂度O(1)，时间复杂度O(n)。

### 解决方案 2 - 双指针
这个解决方案是从leetcode评论区看到的，点赞数量最多的一个Java的解决方案。

基本思路是，从前往后遍历这个字符串，然后使用一个Map存储每一个字符上一次出现的位置。然后我们使用两个指针来表示我们满足条件的最长字串。我们移动快指针来扫描字符串，同时我们更新map。如果这个字符之前出现过，我们会将慢指针向右移动到当前字符上一次出现的位置。此处需要注意，慢指针只能一直向右移动，在这里我们需要把慢指针移动到这个字符上一次出现的位置，而且是和之前位置取的最大值，也就是不能回退，只能往前，否则慢指针和快指针之间的区间里就会有重复的字符。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> lastPresentIndexMap = new HashMap<>();
        
        int max = 0;
        
        for(int slow = -1, fast = 0; fast < s.length(); fast++) {
            if(lastPresentIndexMap.containsKey(s.charAt(fast))) {
                slow = Math.max(slow, lastPresentIndexMap.get(s.charAt(fast)));    //注意此处取的max
            }
            
            lastPresentIndexMap.put(s.charAt(fast), fast);
            
            max = Math.max(max, fast - slow);
        }
        
        return max;
    }
}
```

空间复杂度O(n)，时间复杂度O(n)。