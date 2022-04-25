---
title: "Regular Expression Matching"
date: 2022-04-24T21:05:33+08:00
# post thumb
images:
- "images/leetcode/10-regular-expression-matching.png"
# author
author: "郁金香啊"
# description
description: "Regular Expression Matching"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","困难难度"]
type: "regular"
draft: false
---
初次看到这个题目时，给定测试用例，脑子倒是能够很快计算出是否匹配，但是具体怎么通过代码来进行实现却是一时间没太想清楚。后面看了很多的解决方案，大多数解决方案思路的话基本都是动态规划，此外也了解到一个FAM，有限自动机的概念，这个概念在实现正则表达式时好像还蛮有用的。

给定一个字符串S和一个模式P，实现一个正则匹配，此处我们仅需要支持`.`和`*`。其中，`.`匹配任意字符，`*`匹配零次或者多次前面的元素。注意此处是匹配，不是查找，也就是我们的模式必须恰好匹配整个字符串。

# Regular Expression Matching

**示例 1:**
```markdown
输入: s = "aa", p = "a"
输出: false
解释: 模式只能匹配字符串中的一个字符
```

**示例 2:**
```markdown
输入: s = "aa", p = "a*"
输出: true
解释: *表示匹配前面这个元素一次或者多次，所以我们可以匹配a两次
```

**示例 3:**
```markdown
输入: s = "ab", p = ".*"
输出: true
解释: ".*"意味着我们可以匹配任意字符多次
```

**限制条件:**
* `1 <= s.length <= 20`
* `1 <= p.length <= 30`
* 字符串S只包含小写字母
* 模式P只包含小写字母，`.`以及`*`
* 测试用例保证，在模式P中任意一个`*`必定包含一个有效的前置元素，比如小写字母或者`.`

## 解决方案
### 解决方案 1 - 动态规划
我看到的很多解决方案思路要么是递归要么是动态规划，不过如果你看过我另外一篇文章[动态规划的系统性方法](../../post/system-approach-to-dp/)的话，应该知道这两者本质上是相通的，从递归的解决方案到自顶向下的解决方案再到自底向上的解决方案恰恰就是一套系统性的方法。

我尝试看了几个动态规划的解决方案，但是直接跳跃到动态规划的最终版本，自底向上的解决方案对我来说还是有点难度。不过好在我们知道一套系统性方法，让我们再从头来过，一步步来吧。

老规矩，让我们先回顾下动态规划的系统性方法

```markdown
* 首先，问你自己一个问题，我真的需要使用动态规划解决这个问题吗或者说我真的可以用动态规划解决这个问题吗

* 定义我们的状态
* 定义我们的递归关系，或者说是状态迁移方程
* 列出所有状态转换及其各自状态迁移的条件
* 定义我们的基本情况
* 实现一个朴素的递归解决方案
* 使用自顶向下的方法（Memoization）对我们递归的解决方案进行优化
* 使用自底向上的方法（Tabulation）消除递归的开销
```

#### 可以用动态规划解决这个问题吗
这个题目比较隐晦，说实话特征不是很明显，我说的是什么特征呢，那就是最优解以及优化结构。

其实看到这个题目，很难将它和最优解联系到一起。但是如果我们稍微转变思路其实还是有迹可循的。

首先，限制条件还是蛮清楚的，就是字符串必须满足某一个模式。

其次，那么最优解是指什么呢？在回答这个问题之前，我们先区分一下正则表达式中两个概念，找到(find)和匹配(match)。如果你使用经常使用正则表达式的话，你应该对这两个概念已经非常清楚了。find是指我们的目标字符串中包含给定模式P，而match则是指目标字符串和给定的模式P完全匹配，如果find之后得到的字符串就是整个字符串，或者倒过来说，如果我们的模式P要求匹配字符串的开头和结尾的情况下我们执行find返回true时我们认为此时和match就是完全等价的。

那么我们最优解就是，给定字符串S和模式P，在字符串S中找到满足模式P的最长字串。如果这个最长字串就是我们的字符串S，那么此时就是完全匹配的。

好的，现在我们的正则匹配变成了一个在给定限制条件下求解最优解的问题了，那么这个最优解问题是否包含一个优化结构或者说递归关系呢？

我们先假设我们匹配模式中仅仅包含小写字母和`.`，给定字符串`S[0,m]`以及模式`P[0,n]`，我们可以发现如果`S[0,m]`匹配模式`P[0,n]`，当且仅当
* `(S[0] == P[0] || P[0] == '.') && S[1,m] 匹配 P[1,n]`。

如果匹配模式中再加上`*`的话，整个情况可能相对来说要更加复杂一点
* 当`S[0] == P[0] || P[0] == '.'`
  * 如果`P[1] == "*"`，那么`S[0,m]`匹配模式`P[0,n]`，当且仅当`S[1,m]`匹配`P[0,n]`或者`P[2,n]`，注意此处模式要么没有发生变化，因为模式`P[0]P[1]`也就是`P[0]*`还可以用来继续字符`P[0]`；要么被消耗了两个字符，因为`*`也可以表示匹配任意字符零次。
  * 如果`P[1] != "*"`，那么`S[0,m]`匹配模式`P[0,n]`，当且仅当`S[1,m]`匹配`P[1,n]`，注意此处模式被消耗了一个字符。
* 当`S[0] != P[0] && P[0] != '.'`
  * 如果`P[1] == "*"`，那么`S[0,m]`匹配模式`P[0,n]`，当且仅当`S[0,m]`匹配`P[2,n]`，注意此处模式被消耗了两个字符，因为*也可以表示匹配任意字符零次。
  * 如果`P[1] != "*"`，那么匹配已经失败了，没必要再继续了。
    
我们将层级结构调整一下
* 当`P[1] != "*"`
  * 如果`S[0] == P[0] || P[0] == '.'`，那么`S[0,m]`匹配模式`P[0,n]`，当且仅当`S[1,m]`匹配`P[1,n]`，注意此处模式被消耗了一个字符。
  * 如果`S[0] != P[0] && P[0] != '.'`，那么匹配已经失败了，没必要再继续了。
* 当`P[1] == "*"`
  * 如果`S[0] == P[0] || P[0] == '.'`，那么`S[0,m]`匹配模式`P[0,n]`，当且仅当`S[1,m]`匹配`P[0,n]`或者`P[2,n]`，注意此处模式要么没有发生变化，因为模式`P[0]P[1]`也就是`P[0]*`还可以用来继续字符`P[0]`，要么被消耗了两个字符，因为*也可以表示匹配任意字符零次。
  * 如果`S[0] != P[0] && P[0] != '.'`，那么`S[0,m]`匹配模式`P[0,n]`，当且仅当`S[0,m]`匹配`P[2,n]`，注意此处模式被消耗了两个字符，因为*也可以表示匹配任意字符零次。
    
#### 定义我们的状态
此处的状态就比较好定义了，就是字符串和模式剩余的子串，我们可以通过两个索引来表示，比如sIndex，pIndex，表示我们还需要判断子串`S[sIndex, ]`和模式`P[pIndex, ]`是否匹配。

#### 定义我们的递归关系
此处我们定义一个函数，`isMatch(S, sIndex, P, pIndex)`，其含义是，判断子串`S[sIndex, ]`和模式`P[pIndex, ]`是否匹配。那么我们的递归关系如下
* isMatch(S, sIndex, P, pIndex)
  * `isMatch(S, sIndex, P, pIndex)`等于`P[pIndex + 1] != "*" && (S[sIndex] == P[pIndex] || P[pIndex] == '.') && isMatch(S, sIndex + 1, P, pIndex + 1)`
  * `isMatch(S, sIndex, P, pIndex)`等于`P[pIndex + 1] != "*" && S[sIndex] != P[pIndex] && P[pIndex] != '.'`
  * `isMatch(S, sIndex, P, pIndex)`等于`P[pIndex + 1] == "*" && (S[sIndex] == P[pIndex] || P[pIndex] == '.') && (isMatch(S, sIndex + 1, P, pIndex) || isMatch(S, sIndex + 1, P, pIndex + 2))`
  * `isMatch(S, sIndex, P, pIndex)`等于`P[pIndex + 1] == "*" && S[sIndex] != P[pIndex] && P[pIndex] != '.' && isMatch(S, sIndex, P, pIndex + 2)`

#### 定义基础场景
* 给定空字符串以及空模式，它们是匹配的。
* 给定空模式，如果剩余待匹配的字符串长度大于零，匹配失败。
* 给定空字符串，如果`P[pIndex + 1] = '*'`，不论`P[pIndex]`是什么(`.`还是小写字母)，我们可以跳过`p[0]P[1]`这两个匹配字符，相当于匹配零次。然后我们接着判断`P[pIndex + 2, ]`是否匹配空串，也就是模式是不是类似`a*.*b*`这种.

#### 实现一个朴素的递归解决方案
让我们看下代码实现，代码逻辑相对来说和上面定义的递归关系没有太大差别，唯一需要注意的就是具体实现的时候需要很多边界检查。
```java
class Solution {
    public boolean isMatch(String s, String p) {
        return isMatch(s, 0, p, 0);
    }
    
    private boolean isMatch(String s, int sIndex, String p, int pIndex) {
        if(pIndex >= p.length()) {
            return sIndex >= s.length();
        }
        
        if(pIndex > p.length() - 2 || p.charAt(pIndex + 1) != '*') {
            if(sIndex < s.length() && (s.charAt(sIndex) == p.charAt(pIndex) || p.charAt(pIndex) == '.')) {
                return isMatch(s, sIndex + 1, p, pIndex + 1);
            } else {
                return false;
            }
        } else {
            if(sIndex < s.length() && (s.charAt(sIndex) == p.charAt(pIndex) || p.charAt(pIndex) == '.')) {
                //此处需要注意，即使现在是可以使用P[0]*进行匹配，我们也需要尝试跳过P[0]*使用后面的pattern进行匹配
                //比如aaa与a*a，我们此处的a*只应该匹配到aa，否则pattern中最后一个a将没有内容可以匹配了
                return isMatch(s, sIndex + 1, p, pIndex) || isMatch(s, sIndex, p, pIndex + 2);   
            } else {
                return isMatch(s, sIndex, p, pIndex + 2);
            }
        }
    }
}
```

### 使用缓存对递归解决方案进行优化（Memoization）
此处我们使用一个二维数组对计算结果进行缓存。
```java
class Solution {
    private Boolean[][] dp;
    public boolean isMatch(String s, String p) {
        dp = new Boolean[s.length() + 1][p.length() + 1];

        return isMatch(s, 0, p, 0);
    }

    private boolean isMatch(String s, int sIndex, String p, int pIndex) {
        if(dp[sIndex][pIndex] != null) {
            return dp[sIndex][pIndex];
        }

        if(pIndex >= p.length()) {
            return sIndex >= s.length();
        }
        
        if(sIndex >= s.length()) {
            return pIndex <= p.length() - 2 && p.charAt(pIndex + 1) == '*' && isMatch(s, sIndex, p, pIndex + 2);
        }

        if(pIndex > p.length() - 2 || p.charAt(pIndex + 1) != '*') {
            if(s.charAt(sIndex) == p.charAt(pIndex) || p.charAt(pIndex) == '.') {
                dp[sIndex][pIndex] = isMatch(s, sIndex + 1, p, pIndex + 1);
            } else {
                dp[sIndex][pIndex] = false;
            }
        } else {
            if(s.charAt(sIndex) == p.charAt(pIndex) || p.charAt(pIndex) == '.') {
                dp[sIndex][pIndex] = isMatch(s, sIndex + 1, p, pIndex) || isMatch(s, sIndex, p, pIndex + 2);
            } else {
                dp[sIndex][pIndex] = isMatch(s, sIndex, p, pIndex + 2);
            }
        }

        return dp[sIndex][pIndex];
    }
}
```

### 使用自底向上的方法消除额外的递归开销（Tabulation）
```java
class Solution {
    private Boolean[][] dp;
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();
    
        dp = new Boolean[m + 1][n + 1];
    
        dp[m][n] = true;    //字符串S和模式P均为空串的时候，是匹配的
    
        for(int sIndex = m - 1; sIndex >= 0; sIndex--) {
            dp[sIndex][n] = false;    //模式P为空时，只要字符串S不为空，就不匹配
        }
    
        for(int pIndex = n - 1; pIndex >= 0; pIndex--) {
            //字符串为空时，如果模式满足类似a*a*a*,即所有匹配都认为是匹配零次，那么整体就是匹配的
            dp[m][pIndex] = pIndex <= n - 2 && p.charAt(pIndex + 1) == '*' && dp[m][pIndex + 2];
        }
    
        for(int sIndex = m - 1; sIndex >= 0; sIndex--) {
            for(int pIndex = n - 1; pIndex >= 0; pIndex--) {
                if(pIndex > n - 2 || p.charAt(pIndex + 1) != '*') {
                    if(s.charAt(sIndex) == p.charAt(pIndex) || p.charAt(pIndex) == '.') {
                        dp[sIndex][pIndex] = dp[sIndex + 1][pIndex + 1];
                    } else {
                        dp[sIndex][pIndex] = false;
                    }
                } else {
                    if(s.charAt(sIndex) == p.charAt(pIndex) || p.charAt(pIndex) == '.') {
                        dp[sIndex][pIndex] = dp[sIndex + 1][pIndex] || dp[sIndex][pIndex + 2];
                    } else {
                        dp[sIndex][pIndex] = dp[sIndex][pIndex + 2];
                    }
                }
            }
        }
        
        return dp[0][0];    //dp[0][0]就代表sIndex，pIndex均为零时的情况，也就是整个字符串和整个pattern是否匹配。
    }
}
```