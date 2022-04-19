---
title: "Add Two Number"
date: 2022-04-19T17:44:33+08:00
# post thumb
images:
- "images/leetcode/2-add-two-number.png"
#author
author: "郁金香啊"
# description
description: "Add Two Number"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定两个非空的链表，分别代表两个非负整数，链表的每一个节点代表整数的一位数字，数字是按照倒序存储的。请对这两个数字进行求和，并将结果按照同样的格式进行返回。

此处我们假定，这两个数字不包含任何前置0，当然了除了数字0本身。

# Add Two Number
## 示例

**示例 1:**

```markdown
输入: l1 = [2,4,3], l2 = [5,6,4]
输出: [7,0,8]
解释: 342 + 465 = 807.
```

**示例 2:**

```markdown
输入: l1 = [0], l2 = [0]
输出: [0]
```

**示例 3:**

```markdown
输入: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出: [8,9,9,9,0,0,0,1]
```

**Constraints:**
* 每一个数字的位数也即每个链表的长度限制在范围`[1, 100]`.
* `0 <= Node.val <= 9`
* 保证链表所代表的数字不包含任何前置0

## 解决方案
### 解决方案 1 - 使用内置的Api BigInteger

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        StringBuilder stringBuilder = new StringBuilder();
        while (l1 != null) {
            stringBuilder.append(l1.val);

            l1 = l1.next;
        }
        java.math.BigInteger anInteger = new java.math.BigInteger(stringBuilder.reverse().toString());

        stringBuilder = new StringBuilder();
        while (l2 != null) {
            stringBuilder.append(l2.val);

            l2 = l2.next;
        }
        java.math.BigInteger anotherInteger = new java.math.BigInteger(stringBuilder.reverse().toString());

        java.math.BigInteger resultInteger = anInteger.add(anotherInteger);

        String resultStr = resultInteger.toString();
        int length = resultStr.length();
        ListNode resultNode = null;
        ListNode lastNode = null;
        ListNode currentNode;
        for (int index = 0; index < length; index++) {
            currentNode = new ListNode(resultStr.charAt(length - index - 1) - '0');

            resultNode = resultNode == null ? currentNode : resultNode;

            if (lastNode == null) {
                lastNode = currentNode;
            } else {
                lastNode.next = currentNode;
                lastNode = currentNode;
            }
        }
        return resultNode;
    }
}
```

最简单的实现方案就是使用Java内置的Api，BigInteger来实现。这样的确可以实现，但是整个过程相对比较繁琐。

### 解决方案 2 - 迭代
其实回顾题目，我们其实就是在做加法运算而已，加法运算里面比较关键的一个点就是**进位**，此处我们代码中使用`carry`来表示。由于输入链表本来就是倒序的，所以我们从前往后进行加法运算就好。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode();
        ListNode current = head;
        
        int digit;
        int carry = 0;
        while(l1 != null || l2 != null) {
            digit = carry;
            
            if(l1 != null) {
                digit += l1.val;
                l1 = l1.next;
            }
            
            if(l2 != null) {
                digit += l2.val;
                l2 = l2.next;
            }
            
            current.next = new ListNode(digit % 10);
            current = current.next;
            
            carry = digit / 10;
        }
        
        if(carry != 0) {    //注意此处可能被遗漏
            current.next = new ListNode(carry);
        }
        
        return head.next;
    }
}
```

这里唯一需要注意的一点就是，最后一步，我们可能存在之前的计算导致的溢出问题，也就是两个链表均遍历完成，但是此时`carry = 1`，我们最终还需要再进行一步进位。