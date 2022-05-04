---
title: "Integer to Roman"
date: 2022-04-27T19:59:33+08:00
# post thumb
images:
- "images/leetcode/12-integer-to-roman.png"
# author
author: "郁金香啊"
# description
description: "Integer to Roman"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

罗马数字是由七个不同的字母来表示，I, V, X, L, C, D 和 M.

Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

```markdown
符号       值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

比如，数值`2`在罗马数字表示法中写作`II`，`12`则写作`XII`，就是简单的10（X）加上2(II)，27写作XXVII，就是20（XX）加上5（V）加上2（II）。

罗马数字通常从大往小写，但是数值4对应的罗马数字不是`IIII`，而是`IV`，`V`前面的`I`使用来从数值5中减去1来得到4，同理`IX`表示9。具体规则是
* I（1）可以被放到V（5）和X（10）之前，用来表示4和9
* X（10）可以被放到L（50）和C（100）之前，用来表示40和90
* C（100）可以被放到D（500）和M（1000）之前，用来标识400和900

给定一个阿拉伯数字，将其转换为对应的罗马数字

# Integer to Roman
## 示例
**示例 1:**
```markdown
输入: num = 3
输出: "III"
解释: 3 is represented as 3 ones.
```

**示例 2:**
```markdown
输入: num = 58
输出: "LVIII"
解释: L = 50, V = 5, III = 3.
```

**示例 3:**
```markdown
输入: num = 1994
输出: "MCMXCIV"
解释: M = 1000, CM = 900, XC = 90 and IV = 4.
```

**限制条件:**
* `1 <= num <= 3999`

## 解决方案
### 解决方案 1 - 整除 & 取模
我们罗列出所有可能的表示法，1~9，10~90，100~900，1000，2000，3000，然后我们通过对1000，100，10进行整除和取模运算，就能得到最终的结果。

```java
class Solution {
    public String intToRoman(int num) {
        String[] I = {"","I","II","III","IV","V","VI","VII","VIII","IX"};
        String[] X = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};
        String[] C = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};
        String[] M = {"","M","MM","MMM"};
        
        return M[num / 1000] + C[(num % 1000) / 100] + X[(num % 100) / 10] + I[num % 10];
    }
}
```

## 延伸 - 罗马数字到阿拉伯数字
一个基本规律，如果一个字符表示的数值比后面的那个字符表示的数值小，那么这个数值一定是从后面这个字符表示的数值减去本身锁代表的数值。

```java
class Solution {
    public int romanToInt(String s) {
        if(s.length() == 0) {
            return 0;
        }
        
        Map<Character, Integer> scale = new HashMap<>();
        
        scale.put('I',1);
        scale.put('V',5);
        scale.put('X',10);
        scale.put('L',50);
        scale.put('C',100);
        scale.put('D',500);
        scale.put('M',1000);
        
        int value = 0;
        for(int index = 0;index < s.length() - 1; index++) {
            if(scale.get(s.charAt(index)) < scale.get(s.charAt(index + 1))) {
                value -= scale.get(s.charAt(index));
            } else {
                value += scale.get(s.charAt(index));
            }
        }
        
        value += scale.get(s.charAt(s.length() - 1));
        
        return value;
    }
}
```