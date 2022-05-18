---
title: "Swap Nodes in Pairs"
date: 2022-05-17T20:55:33+08:00
# post thumb
images:
- "images/leetcode/24-swap-nodes-in-pairs.png"
# author
author: "郁金香啊"
# description
description: "Swap Nodes in Pairs"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定一个链表，交换每每两个相邻的节点，然后返回新的链表头。

你的解决方案里不能修改节点值，你只能通过调整节点的顺序来实现。

## 示例
**示例 1:**
```markdown
输入: head = [1,2,3,4]
输出: [2,1,4,3]
```

**示例 2:**
```markdown
输入: head = []
输出: []
```

**示例 3:**
```markdown
输入: head = [1]
输出: [1]
```

**限制条件:**
* 节点值取值范围为[0, 100].
* `0 <= Node.val <= 100`

## 解决方案
### 解决方案 1 - 拆链表 + 合并链表
当我看到这道题目的时候，脑子里面出现了一个很奇特的思路，那就是我们可以先把链表按照奇数节点和偶数节点拆成两个，然后再把他们归并起来，归并的时候第二个链表先归并就好了。

比如假定我们的链表是`1->2->3->4->5->6->7->8`，先按照奇数偶数节点拆分成两个`1->3->5->7`和`2->4->6->8`，然后第二个和第一个链表进行归并就得到`2->1->4->3->6->5->8->7`。

具体代码如下

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
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        
        ListNode first = head, current1 = head;
        ListNode second = head.next, current2 = head.next;
         
        while(current1 != null && current2 != null) {
            if(current2 != null) {
                current1.next = current2.next;
                current1 = current1.next;
            }
            
            if(current1 != null) {
                current2.next = current1.next;
                current2 = current2.next;
            }
        }
        
        ListNode temp = new ListNode();
        head = temp;
        while(first != null || second != null) {
            if(second != null) {
                temp.next = second;
                temp = temp.next;
                second = second.next;
            }
            
            if(first != null) {
                temp.next = first;
                temp = temp.next;
                first = first.next;
            }
        }
        
        return head.next;
    }
}
```

### 解决方案 2 - 递归
上面这个解决方案虽然解决了问题，但是如果我们不是每两个相邻节点调换位置而是三个或者更多个相邻节点调换位置的话，就很难实现了。

我们可以使用递归来解决这个问题，我们先取下来前两个节点，比如，1，2，然后我们将2指向1，1则指向3之后的链表以同样方式翻转的结果。

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        
        ListNode next = head.next;   //head = 1, head.next = 2, next = 2, next.next = 3...
        head.next = swapPairs(head.next.next);    //head = 1, head.next = 3..., next = 2, next.next = 3...
        next.next = head;    //head = 1, head.next = 3..., next = 2, next.next = 1
        
        return next; //next = 2, next.next = 1, next.next.next = 3... 
    }
}
```

### 解决方案 3 - 迭代
```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode virtual = new ListNode(0);
        virtual.next = head;
        ListNode point = virtual;
        
        while (point.next != null && point.next.next != null) {
            ListNode swap1 = point.next;
            ListNode swap2 = point.next.next;
            
            point.next = swap2;
            swap1.next = swap2.next;
            swap2.next = swap1;
            point = swap1;
        }
        
        return virtual.next;
    }
}
```