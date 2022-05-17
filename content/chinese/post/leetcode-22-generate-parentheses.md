---
title: "Valid Parentheses"
date: 2022-05-16T20:13:33+08:00
# post thumb
images:
- "images/leetcode/22-generate-parentheses.png"
# author
author: "郁金香啊"
# description
description: "Generate Parentheses"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---
给定N组括号，实现一个函数以生成所有可能的有效的括号组合。

## 示例
**示例 1:**
```markdown
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

**示例 2:**
```markdown
Input: n = 1
Output: ["()"]
```

**限制条件:**
* `1 <= n <= 8`

## 实现方案
### 实现方案 1 - 展开 - 错误答案
当我看到这道题目时，我第一个解决方案是对括号进行一层一层的展开，如果我们有一个字符串了，那么再加一组括号的话，要么是在这个字符串的前面加上`()`，要么是在这个字符串的后面加上`()`，要么是在前面加上`(`然后在后面加上`)`.

在数量较少时这样的思路的确能够得到正确的结果，但是括号的组数为4时我们实际得到的结果就已经有些遗漏了。我们无法组装出`(())(())`这种结果。

我们简单看下四次计算过程。注意我们去掉了重复的结果

```markdown
()
()() (())
()()() (()()) ()(()) ((())) (())()
()()()() (()()()) ()(()()) ((()())) (()())() ()()(()) (()(())) ()(())() ()((())) (((()))) ((()))() ((())()) (())()()
```

```java
class Solution {
    //(())(())  cannot generate this combination
    public List<String> generateParenthesis_wrong(int n) {
        LinkedList<String> queue = new LinkedList<>();

        queue.offer("()");

        for (int index = 1; index < n; index++) {
            int size = queue.size();
            String element;
            for (int i = 0; i < size; i++) {
                element = queue.poll();

                queue.offer("()" + element);
                queue.offer("(" + element + ")");
                queue.offer(element + "()");
            }
        }

        return queue.stream().distinct().collect(Collectors.toList());
    }
}
```

### 解决方案 2 - 递归
想了很久但是最终解决方案有问题之后我就看了leetcode提供的解决方案了，大家提到比较多的是递归，相对于其他解决方案来说比较好理解。

此处我们思考下，什么情况下我们的字符串序列不再是一个有效的括号序列了呢。我们从左往右进行扫描，只要有任意一个位置，在这个位置，我们发现当前位置包含当前位置左边的部分左括号的数量甚至少于右括号的数量了，那么我们永远不可能基于当前的结果得到一个有效的字符序列了。

所以我们只需要控制我们在当前字符序列上进行扩充时左括号数量永远大于等于右括号的数量，我们就一定能够在后续的扩充过程中组装出正确的字符序列。

当然了，此处我们的左右括号还有另一个数量限制，就是小于n。让我们看下代码实现。
```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        
        generate(res, "", n,0,0);
        
        return res;
    }
    
    private void generate(List<String> results, String prefix, int n, int open, int close) {
        if(n == open && open == close) {
            results.add(prefix);
        }
        
        if(n > open) {    //此处摆正左括号数量不超过n个
            generate(results, prefix + "(", n, open + 1, close);
        }
        
        if(open > close) {    //此处保证右括号数量不超过左括号数量
            generate(results, prefix + ")", n, open, close + 1);
        }
    }
}
```

### 解决方案 3 - 递归 + 迭代
这个解决方案是在网上看到的，另一种方式的递归。

我们来观察任意一个合法的括号序列，首先第一个位置的字符一定是`(`，然后我们向右遍历，当我们第一次遇到一个右括号且满足左括号数量等于右括号数量时，我们将此位置记作`c`。那么我们可以得出`S[0,c]`是一个合法的序列，且`S[c + 1， 2n - 1]`也一定是一个合法的序列。

因为我们有`n`组括号，假设`S[1,c - 1]`之间是`a`组括号，那么`S[c + 1， 2n - 1]`之间就是`n - a - 1`组括号。具体情况见图1。

{{< image src="images/leetcode/22-generate-parentheses/1.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}

所以，我们要求`n`组括号的所有可能组合，只需要找到所有的`a`组括号对应的组合以及其对应的`n - a - 1`组括号对应的组合然后再相互组合即可。

假定此处`n`等于`3`，我们把`0`到`c`之间的括号组数记为`a`，`c + 1`到最后面的括号组数记为`b`。那么，`a`，`b`一共有三种可能的情况。
* a = 0 & b = 2
* a = 1 & b = 1
* a = 2 & b = 0

#### a = 0 & b = 2
其包含两种情况，可能是`(())`也可能是`()()`。所以最终的结果就是`()(())`或者`()()()`，详情见下图。

{{< image src="images/leetcode/22-generate-parentheses/2.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}
{{< image src="images/leetcode/22-generate-parentheses/3.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}

#### a = 1 & b = 1
只包含一种情况，因为括号组数为1时，只可能是一种情况`()`，所以最终结果是`(())()`，详情见下图
{{< image src="images/leetcode/22-generate-parentheses/4.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}

#### a = 2 & b = 0
同理因为括号组数为2时，因为括号组数为2时，可能是`(())`也可能是`()()`。所以最终的结果就是`((()))`或者`(()())`，详情见下图。
{{< image src="images/leetcode/22-generate-parentheses/5.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}
{{< image src="images/leetcode/22-generate-parentheses/6.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}

#### 代码实现
```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList();
        
        for (int a = 0; a < n; a++) {
            for (String left: generateParenthesis(a)) {
                for (String right: generateParenthesis(n - 1 - a)) {
                    res.add("(" + left + ")" + right);
                }
            }
        }
        
        return res;
    }
}
```

### 扩展 - 卡塔兰数
如果这道题目并不需要所有最终的结果而是只需要一个数量，我们其实有另一种更方便的方法来计算。

我们来观察一下当我们的括号组数是1到4的时候可能的结果数量。
```markdown
_  1
()  1 
()() (())  2 = f(0) * f(1) + f(1) * f(0)
()()() (()()) ()(()) ((())) (())()  5 = f(0) * f(2) + f(1) * f(1) + f(2) * f(0)
()()()() (()()()) ()(()()) ((()())) (()())() ()()(()) (()(())) ()(())() ()((())) (((()))) ((()))() ((())()) (())()() (())(())  14 = f(0) * f(3) + f(1) * f(2) + f(2) * f(1) + f(3) * f(0)
```

注意此处第一组数据，当我们的括号组数为0的时候，我们实际的结果仍然包含一种可能的结果，那就是一个空串。所以此处我们有`f(0) = 1`。

然后在稍微整理一下我们的结果，

```markdown
f(0) = 1
f(1) = 1
f(2) = f(0) * f(1) + f(1) * f(0) = 2
f(3) = f(0) * f(2) + f(1) * f(1) + f(2) * f(0) = 5
f(4) = f(0) * f(3) + f(1) * f(2) + f(2) * f(1) + f(3) * f(0) = 14
```

这个数列的规律其实就是卡塔兰数。我们这里只是使用很少的测试用例来观察了一下并得出这个结论，如果你想了解怎么证明它的确是一个卡塔兰数列，可以看 [这里](http://lanqi.org/interests/10939/) 或者 [这里](https://leetcode.wang/leetCode-22-Generate-Parentheses.html) 。

如果你想要了解更多的卡塔兰数的内容，可以参考 [维基百科](https://zh.m.wikipedia.org/zh-hans/%E5%8D%A1%E5%A1%94%E5%85%B0%E6%95%B0) 的定义。

{{< image src="images/leetcode/22-generate-parentheses/7.png" caption="" alt="alter-text" height="" width="" position="center" command="fill" option="q100" class="img-fluid" title="image title" webp="false" >}}

在 [这篇文章](https://leetcode.wang/leetCode-22-Generate-Parentheses.html) 中的扩展部分，作者还罗列了其他的一些卡塔兰数的例子
* 2n 个人排队买票，其中 n 个人持 50 元，n 个人持 100 元。每张票 50 元，且一人只买一张票。初始时售票处没有零钱找零。请问这 2n 个人一共有多少种排队顺序，不至于使售票处找不开钱？
* 对于一个无限大的栈，一共n个元素，请问有几种合法的入栈出栈形式？
* P = a1 a2 a3 ... an，其中 ai 是矩阵。根据乘法结合律，不改变矩阵的相互顺序，只用括号表示成对的乘积，试问一共有几种括号化方案？
* n 个结点可构造多少个不同的二叉树？

最后一个有一个对应leetcode题目，之前碰巧刷到过，之后会专门写对应的博文进行分析。
