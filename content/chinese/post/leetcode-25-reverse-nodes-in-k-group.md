---
title: "Reverse Nodes in k-Group"
date: 2022-05-18T20:44:33+08:00
# post thumb
images:
- "images/leetcode/25-reverse-nodes-in-k-group.png"
# author
author: "郁金香啊"
# description
description: "Reverse Nodes in k-Group"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","困难难度"]
type: "regular"
draft: false
---
给定一个链表，从前往后，每k个节点做一次翻转，返回翻转后的链表。

k是一个正整数，其小于或者等于链表长度，如果链表的节点数量n不是k的整数倍，那么最后面的`n % k`个节点顺序保持不变。

你不能改变节点的数值，只能调整节点的顺序。

## 示例
**示例 1:**
```markdown
输入: head = [1,2,3,4,5], k = 2
输出: [2,1,4,3,5]
```

**示例 2:**
```markdown
输入: head = [1,2,3,4,5], k = 3
输出: [3,2,1,4,5]
```

**限制条件:**
* 给定节点数量n
* `1 <= k <= n <= 5000`
* `0 <= Node.val <= 1000`

## 解决方案
### 解决方案 1 - 快慢指针 + 栈
网上有很多解决方案是基于递归或者迭代的，递归的还比较好理解，但是迭代的解决方案中包含大量的指针运算，不是很好理解。

我第一次看到这个问题时，偶然想到，如果是翻转两个节点，我们使用指正运算还比较好理解，但是如果需要翻转更多的节点，我们如果用一个栈来做这个事情可能最合适不过了。

但是我们还是需要做一些处理
* 我们每k个节点进行一次翻转，那么我们就需要先找到一组一组的k个节点。此处我们使用快慢指针来找每一组k个节点。
* 最后面的`n % k`需要保持原来的顺序不变。如果我们找最后一组k个节点，发现我们找不到k个就已经到达了链表结尾，那么我们把慢指针直接append到新链表末尾就好了。

让我们看下代码实现。

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
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode fast = head;
        ListNode slow = head;
        ListNode res = null;
        ListNode current = null;
        Stack<ListNode> stack = new Stack<>();
        while(fast != null && slow != null) {
            int index = 0;
            for(;index < k && fast != null; index++) {
                fast = fast.next;
            }
            
            if(index != k) {
                break;
            }
            
            for(int i = 0; i < k; i++) {
                stack.push(slow);
                slow = slow.next;
            }
            
            for(int i = 0;i < k; i++) {
                if(res == null) {
                    current = res = stack.pop();
                    
                    continue;
                }
                
                current.next = stack.pop();
                current = current.next;
                current.next = null;
            }
        }
        
        if(slow != null) {
            current.next = slow;
        }
        
        return res;
    }
}
```