---
title: "Letter Combinations of a Phone Number"
date: 2022-05-10T19:45:33+08:00
# post thumb
images:
- "images/leetcode/17-letter-combinations-of-a-phone-number.png"
# author
author: "郁金香啊"
# description
description: "Letter Combinations of a Phone Number"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定一个字符串其包含2到9的数字，返回所有可能的字母组合。此处的字母组合是指使用九键输入法时可能出现的组合。

## 示例
**示例 1:**
```markdown
输入: digits = "23"
输出: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**示例 2:**
```markdown
输入: digits = ""
输出: []
```

**示例 3:**
```markdown
输入: digits = "2"
输出: ["a","b","c"]
```

**限制条件:**
* `0 <= digits.length <= 4`
* `数字取值范围为['2', '9']`

## 解决方案
### 解决方案 1 - 队列
其实我们观察下最终结果，给定一个数字的话，我们返回这个数字对应按键上的字母就好了，如果有两个数字的话，我们需要对前一个数字的结果再进行展开，或者说组合。

利用队列我们很容易做到这一点。

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public List<String> letterCombinations(String digits) {
        LinkedList<String> res = new LinkedList<>();
        
        res.add("");
        
        String[] keyBoard = {
            "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
        };

        String prefix;
        for (char digit: digits.toCharArray()) {
            int size = res.size();    //此处是我们需要展开的数量
            
            for (int index = 0;index < size; index++) {
                prefix = res.poll();
                for (char c: keyBoard[digit - '0'].toCharArray()) {
                    res.offer(prefix + c);    //注意此处会改变size
                }
            }
        }
        
        return res;
    }
}
```

### 解决方案 2 - 递归
在上一个解决方案分析的时候，我们就说过，最简单的场景是只有一个数字的情况，我们将这个数字对应的键盘的上字符拆分成字符串返回就好了。

然后如果有两个数字，我们只需要在原有的基础上进行一次展开，三个数字，四个数字，以此类推。我们可以发现，这个过程其实倒过来看的话就是一个递归的过程，递归的终止条件就是只有一个数字的情况。

```java
import java.util.ArrayList;

class Solution {
    private static String[] keyBoard = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };

    public List<String> letterCombinations(String digits) {
        List<String> res = new ArrayList<>();

        helper(res, digits, 0, "");

        return res;
    }

    private void helper(List<String> res, String digits, int index, String prefix) {
        if (index == digits.length()) {
            res.add(prefix);
            
            return;
        }

        for (char c : keyBoard[digits.charAt(index) - '0'].toCharArray()) {
            helper(res, digits, index + 1, prefix + c);
        }
    }
}
```

