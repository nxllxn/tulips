---
title: "Zigzag Conversion"
date: 2022-04-23T18:44:33+08:00
# post thumb
images:
- "images/leetcode/6-zigzag-conversion.png"
# author
author: "郁金香啊"
# description
description: "Zigzag Conversion"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

我们来定义一个zigzag模式，给定一个字符串，比如`PAYPALISHIRING`，特定条件下，它的zigzag模式为`PAHNAPLSIIGYIR`，这是我们将原字符串按照一定的规则打破重组之后得到的。

具体规则是，首先将这个字符串中的各个字符按照英文字母N的书写顺序在特定的行数限制下进行排列，然后我们收集每一行的字符，并按照行从上往下将各行中收集到的字符串拼接成一个新的字符串。

比如，对于字符串`PAYPALISHIRING`，首先按照字母N的书写顺序在三行上进行重新排列，将得到
```markdown
P   A   H   N
A P L S I I G
Y   I   R
```

然后我们将每一行上的字符收集起来，得到
```markdown
PAHN
APLSIIG
YIR
```

并按照从上往下的顺序将多行收集到的字符串拼接成一个新的字符串`PAHN` + `APLSIIG` + `YIR` => `PAHNAPLSIIGYIR`。

同理，如果行数为4，那么重新排列之后是
```markdown
P     I    N
A   L S  I G
Y A   H R
P     I
```

重新收集拼接之后将会是`PIN` + `ALSIG` + `YAHR` + `PI` +  =>`PINALSIGYAHRPI`。

# Zigzag Conversion
## 示例

**示例 1:**
```markdown
输入: s = "PAYPALISHIRING", numRows = 3
输出: "PAHNAPLSIIGYIR"
```

**示例 2:**
```markdown
输入: s = "PAYPALISHIRING", numRows = 4
输出: "PINALSIGYAHRPI"
```

示例 3:
```markdown
输入: s = "A", numRows = 1
输出: "A"
```

**限制:**
* `1 <= s.length <= 1000`
* 字符串中包含英文大小写字母以及`,`和`.`
* `1 <= numRows <= 1000`

## 解决方案
### 解决方案 1 - 往返游标
此处我们关键是需要知道每一次我放置一个新的字符的时候，我应该将其放置到哪一行。

其实我们仔细思考就会发现，放置的行数在一个区间内不断往返，以三行为例，行数是`1，2，3，2，1，2，3，2，1。。。`，如此循环往复。所以我们这里关键是在行数到达最大行数和行数到达最小行数的时候完成一次折返。

```java
class Solution {
      public String convert(String s, int numRows) {
        if(numRows == 1) {
            return s;
        }
        
        StringBuilder[] matrix = new StringBuilder[numRows];
        
        for(int index = 0;index < numRows; index++) {
            matrix[index] = new StringBuilder();
        }
        
        int cursor = 0;
        int direction = 1;
        for(int index = 0;index < s.length();index++) {
            matrix[cursor].append(s.charAt(index));
            
            cursor += direction;
            
            if(cursor == numRows) {    //到达最下面一行，折返，往上走
                cursor = numRows - 2;
                direction = -1;
            }
            
            if(cursor == -1) {    //到达最上面一行，折返，往下走
                cursor = 1;
                direction = 1;
            }
        }
        
        StringBuilder sb = new StringBuilder();
        for(StringBuilder item:matrix) {
            sb.append(item);
        }
        
        return sb.toString();
    }
}
```