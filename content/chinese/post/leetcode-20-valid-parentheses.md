---
title: "Valid Parentheses"
date: 2022-05-11T19:40:33+08:00
# post thumb
images:
- "images/leetcode/20-valid-parentheses.png"
# author
author: "郁金香啊"
# description
description: "Valid Parentheses"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---
给定一个由'(', ')', '{', '}', '[' and ']'组成的字符串，判断这个字符串里的括号是否匹配。

## 示例
**示例 1:**
```markdown
输入: s = "()"
输出: true
```

**示例 2:**
```markdown
输入: s = "()[]{}"
输出: true
```

**示例 3:**
```markdown
输入: s = "(]"
输出: false
```

**限制条件:**
* `1 <= s.length <= 104`
* 字符串只包含字符`()[]{}`

## 解决方案
### 解决方案 1 - 栈
我们将所有的左括号入站，每当遇到右括号，我们判断栈顶的左括号和当前的右括号是否匹配，如果匹配那么就将栈顶的左括号出栈。如果出现不匹配的情况或者遍历结束之后栈不为空，那么这个字符串中的括号序列是不匹配的。

```java
class Solution {
    public boolean isValid(String s) {    //1）可以加上奇数长度的校验2）遇到(,[,{对应push),],}.非(,[,{时直接pop比较就好了，比peek少一次比较
        Stack<Character> stack = new Stack<>();
        int index = 0;
        for(char c:s.toCharArray()){
            if(stack.isEmpty()){
                stack.push(c);
                continue;
            }
            
            if(c == ')' && stack.peek() == '('){
                stack.pop();
                continue;
            }
            
            if(c == '}' && stack.peek() == '{'){
                stack.pop();
                continue;
            }
            
            if(c == ']' && stack.peek() == '['){
                stack.pop();
                continue;
            }
            
            stack.push(c);
        }
        
        return stack.isEmpty();
    }
}
```