---
title: "Longest Palindromic Substring"
date: 2022-04-21T12:01:33+08:00

# post thumb

images:

- "images/leetcode/5-longest-palindromic-substring.png"

# author

author: "郁金香啊"

# description

description: "Longest Palindromic Substring"

# Taxonomies

categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定一个字符串，返回其最长回文字串。

# The Longest Palindromic Substring

## 示例

**示例 1:**

```markdown
输入: s = "babad"
输出: "bab"
解释: "aba"也是一个正确的答案
```

**示例 2:**

```markdown
输入: s = "cbbd"
输出: "bb"
```

**限制条件:**

* `1 <= s.length <= 1000`
* 字符串中仅仅包含数组和字母

## 解决方案

对于这道题目，leetcode自己给出了五个解决方案，这里我们选择其中两个解决方案来进行分析。

### 解决方案 1 - 动态规划

我们前面刚好写了一篇关于动态规划的文章，感兴趣的同学可以先看看这篇文章 - [动态规划的系统性方法](../../post/system-approach-to-dp/)。接下来我们就按照这篇文章里面的阐述的方法来对这个问题进行分析。

让我们回顾下动态规划的系统性方法

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

这个问题同样是让我们基于一个特定的条件求解一个最大的结果，条件是回文序列，结果是最大字串长度。我们可以看出这是一个最优解问题，那么它是否包含一个优化结构呢？

让我们看看回文序列，一个字符序列S[i,j]是回文序列，当且仅当，S[i] == S[j]且S[i + 1, j - 1]也是一个回文序列。

让我们再看看最大回文序列，如果字符序列S[i,j]是回文序列，那么其长度就是它所有可能的字串中最长的；如果它不是回文序列，那么它的字串中的回文序列，要么出现在S[i + 1][j]，要么出现在S[i][j - 1]
。我们的最长字串就是这三种情况中的最大值。

#### 定义我们的状态

显然此处影响到字串是否是一个回文字符串的因素就是字串的两个边界，所以此处我们的两个状态就是左右边界i和j。

#### 定义我们的递归关系

此处我们定义一个函数palindromic(S,i,j)，其含义是判断字符序列S[i,j]是否是一个回文序列。

* `palindromic(S,i,j) = S[i] == S[j] && palindromic(S,i + 1,j - 1)`

我们再定义一个函数maxPalindromicLength(S,i,j)，其含义是计算字符序列S[i,j]中最长回文序列的长度。

* `maxPalindromicLength(S,i,j) = j - i + 1`如果`palindromic(S,i,j) == true`
* `maxPalindromicLength(S,i,j) = Math.max(maxPalindromicLength(S,i + 1,j), maxPalindromicLength(S,i,j - 1))`
  如果`palindromic(S,i,j) == false`

#### 定义基础场景

* `palindromic(S,i,i) = true`
* `palindromic(S,i,i + 1) = S[i] == S[i + 1]`
* `maxPalindromicLength(S,i,i) == 1`

#### 实现一个朴素的递归解决方案

```java
public class Solution {
    int maxPalindromeStrCalledCount = 0;
    int isPalindromeCalledCount = 0;

    public String longestPalindrome(String s) {
        int[] res = maxPalindromeStr(s, 0, s.length() - 1);

        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于67108863
        System.out.println("maxPalindromeStrCalledCount:" + maxPalindromeStrCalledCount);
        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于67108863
        System.out.println("isPalindromeCalledCount:" + isPalindromeCalledCount);

        return s.substring(res[0], res[1] + 1);
    }

    private int[] maxPalindromeStr(String s, int i, int j) {
        maxPalindromeStrCalledCount++;
        if (isPalindrome(s, i, j)) {
            return new int[]{i, j};
        }

        int[] left = maxPalindromeStr(s, i, j - 1);
        int[] right = maxPalindromeStr(s, i + 1, j);

        return left[1] - left[0] > right[1] - right[0] ? left : right;
    }

    private boolean isPalindrome(String s, int i, int j) {
        isPalindromeCalledCount++;
        if (i == j) {
            return true;
        }

        if (i == j - 1) {
            return s.charAt(i) == s.charAt(j);
        }

        return s.charAt(i) == s.charAt(j) && isPalindrome(s, i + 1, j - 1);
    }
}
```

为了展示各种解决方案之间的差别，此处我们打印了两个方法的调用次数（这里其实是指实际执行整个方法计算逻辑的次数，下面的优化中，有些方法调用没有被计数，因为命中了缓存，所以直接返回就没有算作调用了）。

可以看到给定字符串`abcdefghijklmnopqrstuvwxyz`时，两个方法调用次数达到了惊人的`67108863`次，六千七百万次！！！

这个答案在leetcode上，给定字符串`babaddtattarrattatddetartrateedredividerb`，执行甚至直接超时了，不过无所谓，让我们来对它进行一些优化。

### 使用缓存对递归解决方案进行优化（Memoization）

注意看，我们这里其实有两个递归，一个是判断当前字串是否是一个回文序列，一个是递归的去计算最长的回文字串。这两个递归流程其实都可以进行优化。

计算回文序列的递归我们很容易使用缓存进行优化，因为`palindromic(S,i,j)`依赖于`palindromic(S,i + 1,j - 1)`。

然而计算最长回文字串的递归的优化就不那么直观了，为了便于理解，此处我们先对一个相对简单却又足够的复杂的样例的递归过程做一个展开。

假定我们有一个长度为5的字符串，且这五个字符字符均不相同，我们对递归过程展开后的结果可能是这样的，为了方便理解，我们使用0到4的索引来进行分析。

```markdown
01234 
0123             1234 
012     123      123     234 
01  12  12  23   12  23  23  34 
0 1 1 2 1 2 2 3  1 2 2 3 2 3 3 4 
```

我们可以发现这里面有大量的重叠子问题。这也是为什么给定一个长度50甚至是40的字符串，这段代码将完全无法计算出结果。

好的，知道了问题所在，现在我们就对其进行优化吧。首先优化计算是否是回文序列的递归。

```java
public class Solution {
    int maxPalindromeStrCalledCount = 0;
    int isPalindromeCalledCount = 0;
    Boolean[][] dp;

    public String longestPalindrome(String s) {
        dp = new Boolean[s.length()][s.length()];

        int[] res = maxPalindromeStr(s, 0, s.length() - 1);

        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于67108863
        System.out.println("maxPalindromeStrCalledCount:" + maxPalindromeStrCalledCount);
        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于351
        System.out.println("isPalindromeCalledCount:" + isPalindromeCalledCount);

        return s.substring(res[0], res[1] + 1);
    }

    private int[] maxPalindromeStr(String s, int i, int j) {
        maxPalindromeStrCalledCount++;
        if (isPalindrome(s, i, j)) {
            return new int[]{i, j};
        }

        int[] left = maxPalindromeStr(s, i, j - 1);
        int[] right = maxPalindromeStr(s, i + 1, j);

        return left[1] - left[0] > right[1] - right[0] ? left : right;
    }

    private boolean isPalindrome(String s, int i, int j) {
        if (dp[i][j] != null) {
            return dp[i][j];
        }

        isPalindromeCalledCount++;

        if (i == j) {
            dp[i][j] = true;
        } else if (i == j - 1) {
            dp[i][j] = s.charAt(i) == s.charAt(j);
        } else {
            dp[i][j] = s.charAt(i) == s.charAt(j) && isPalindrome(s, i + 1, j - 1);
        }

        return dp[i][j];
    }
}
```

接下来我们对第二个递归进行优化，此处我们使用map进行缓存。

```java
import java.util.HashMap;

public class Solution {
    int maxPalindromeStrCalledCount = 0;
    int isPalindromeCalledCount = 0;
    Boolean[][] dp;
    Map<Integer, int[]> ddpp;

    public String longestPalindrome(String s) {
        dp = new Boolean[s.length()][s.length()];
        ddpp = new HashMap<>();

        int[] res = maxPalindromeStr(s, 0, s.length() - 1);

        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于351
        System.out.println("maxPalindromeStrCalledCount:" + maxPalindromeStrCalledCount);
        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于 351
        System.out.println("isPalindromeCalledCount:" + isPalindromeCalledCount);

        return s.substring(res[0], res[1] + 1);
    }

    private int[] maxPalindromeStr(String s, int i, int j) {
        int key = (i << 16) + j;

        if (ddpp.containsKey(key)) {
            return ddpp.get(key);
        }

        maxPalindromeStrCalledCount++;
        if (isPalindrome(s, i, j)) {
            ddpp.put(key, new int[]{i, j});
        } else {
            int[] left = maxPalindromeStr(s, i, j - 1);
            int[] right = maxPalindromeStr(s, i + 1, j);

            ddpp.put(key, left[1] - left[0] > right[1] - right[0] ? left : right);
        }

        return ddpp.get(key);
    }

    private boolean isPalindrome(String s, int i, int j) {
        if (dp[i][j] != null) {
            return dp[i][j];
        }

        isPalindromeCalledCount++;

        if (i == j) {
            dp[i][j] = true;
        } else if (i == j - 1) {
            dp[i][j] = s.charAt(i) == s.charAt(j);
        } else {
            dp[i][j] = s.charAt(i) == s.charAt(j) && isPalindrome(s, i + 1, j - 1);
        }

        return dp[i][j];
    }
}
```

注意，此处我们使用起始索引左移16位加上截止索引来作为缓存的key，可以发现整体的实际函数调用次数已经有了非常明显的减少。

### 使用自底向上的方法消除额外的递归开销（Tabulation）

为了使用自底向上的方式进行优化，我们先进行一个简单的处理，使用三维数组而不是Map来做缓存。

```java
import java.util.HashMap;

public class Solution {
    int maxPalindromeStrCalledCount = 0;
    int isPalindromeCalledCount = 0;
    Boolean[][] dp;
    int[][][] ddpp;

    public String longestPalindrome(String s) {
        dp = new Boolean[s.length()][s.length()];
        ddpp = new int[s.length()][s.length()][];

        int[] res = maxPalindromeStr(s, 0, s.length() - 1);

        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于351
        System.out.println("maxPalindromeStrCalledCount:" + maxPalindromeStrCalledCount);
        //使用测试数据 'abcdefghijklmnopqrstuvwxyz' ，最终count等于 351
        System.out.println("isPalindromeCalledCount:" + isPalindromeCalledCount);

        return s.substring(res[0], res[1] + 1);
    }

    private int[] maxPalindromeStr(String s, int i, int j) {
        if (ddpp[i][j] != null) {
            return ddpp[i][j];
        }

        maxPalindromeStrCalledCount++;
        if (isPalindrome(s, i, j)) {
            ddpp[i][j] = new int[]{i, j};
        } else {
            int[] left = maxPalindromeStr(s, i, j - 1);
            int[] right = maxPalindromeStr(s, i + 1, j);

            ddpp[i][j] = left[1] - left[0] > right[1] - right[0] ? left : right;
        }

        return ddpp[i][j];
    }

    private boolean isPalindrome(String s, int i, int j) {
        if (dp[i][j] != null) {
            return dp[i][j];
        }

        isPalindromeCalledCount++;

        if (i == j) {
            dp[i][j] = true;
        } else if (i == j - 1) {
            dp[i][j] = s.charAt(i) == s.charAt(j);
        } else {
            dp[i][j] = s.charAt(i) == s.charAt(j) && isPalindrome(s, i + 1, j - 1);
        }

        return dp[i][j];
    }
}
```

然后我们通过自底向上的方式进行优化。

```java
import java.util.HashMap;

public class Solution {
    Boolean[][] dp;
    int[][][] ddpp;

    public String longestPalindrome(String s) {
        dp = new Boolean[s.length()][s.length()];
        ddpp = new int[s.length()][s.length()][];

        for (int index = 0; index < s.length(); index++) {
            dp[index][index] = true;
            ddpp[index][index] = new int[]{index, index};
        }

        for (int index = 0; index < s.length() - 1; index++) {
            dp[index][index + 1] = s.charAt(index) == s.charAt(index + 1);
        }

        for (int j = 2; j < s.length(); j++) {
            for (int i = 0; i < s.length() - j; i++) {
                dp[i][i + j] = s.charAt(i) == s.charAt(i + j) && dp[i + 1][i + j - 1];
            }
        }

        int[] res = new int[]{0, 0};
        for (int j = 1; j < s.length(); j++) {
            for (int i = 0; i < s.length() - j; i++) {
                if (dp[i][i + j]) {
                    ddpp[i][i + j] = new int[]{i, i + j};
                } else {
                    ddpp[i][i + j] = ddpp[i + 1][i + j][1] - ddpp[i + 1][i + j][0] > ddpp[i][i + j - 1][1] - ddpp[i][i + j - 1][0]
                        ? ddpp[i + 1][i + j] : ddpp[i][i + j - 1];
                }

                if (ddpp[i][i + j][1] - ddpp[i][i + j][0] > res[1] - res[0]) {
                    res = ddpp[i][i + j];
                }
            }
        }

        return s.substring(res[0], res[1] + 1);
    }
}
```

其实这道题目在评论区也有一个动态规划的解决方案，只用了一层动态规划加两个for循环就解决了这个问题，而且实际运行结果耗时只有当前这个方案的四分之一。

我们这里其实反而做了很多无效的计算，比如长度100的回文串，使用递归时如果我们发现它已经是一个回文串了，它一定是它所有回文字串中最长的那个，其实就不会再去判断[0,99]和[1,100]是不是最长回文串了。

但是使用Tabulation的时候，我们总是从最底部开始计算，也就是先计算所有较短的字符串中的最大回文串，导致最终实际上进行了很多无效的计算，

**如此看来，在一些有递归分支裁剪的场景里面，自底向上的解决方案最终的效果可能反而比自顶向下的解决方案要差。**

#### 解决方案 2 - 扩展中心

让我们再用用[上一个问题](../../post/leetcode-4-medianof-two-sorted-arrays)中用到的切割法，给定任意字符串S，我们为其定义一些虚拟位置，这些位置位于任意两个元素之间，看起来可能像这样，

```markdown
[a b c d]  ->   [a # b # c # d]      (N = 4)
position index 0 1 2 3 4 5 6      (N_Position = 7)

[z x c v b]->   [z # x # c # v # b]     (N = 5)
position index 0 1 2 3 4 5 6 7 8    (N_Position = 9)
```

然后我们分别以每一个元素以及刚刚定义好的虚拟位置同时向两边扩展，如果两边的字符相同，那么我们当前扩展的区域就是回文串，我们会继续扩展，否则结束扩展。

如果是以一个元素为扩展中心来扩展，最终得到的回文串是奇数长度的；如果以一个虚拟位置为扩展中心来扩展，最终得到的回文串是偶数长度的。

```java
public class Solution {
    public String longestPalindrome(String s) {
        int[] res = new int[]{0, 0};

        for (int index = 0; index < s.length(); index++) {    //从元素开始扩展
            int[] expandFromElement = expandAroundCenter(s, index, index);

            if (expandFromElement[1] - expandFromElement[0] > res[1] - res[0]) {
                res = expandFromElement;
            }
        }

        for (int index = 0; index < s.length() - 1; index++) {    //从虚拟位置，也就是间隙开始扩展
            int[] expandFromGap = expandAroundCenter(s, index, index + 1);

            if (expandFromGap[1] - expandFromGap[0] > res[1] - res[0]) {
                res = expandFromGap;
            }
        }

        return s.substring(res[0], res[1] + 1);
    }

    private int[] expandAroundCenter(String s, int i, int j) {
        while (i >= 0 && j < s.length() && s.charAt(i) == s.charAt(j)) {
            i--;
            j++;
        }

        return new int[]{i + 1, j - 1};    //注意此处多扩展了一次，需要回退
    }
}
```

代码非常简单明了，时间复杂度O(n * n)，空间复杂度O(n)，在leetcode上跑完所有的测试用例只需要几十毫秒。