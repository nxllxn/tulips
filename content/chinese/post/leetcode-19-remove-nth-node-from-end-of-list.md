---
title: "Remove Nth Node From End of List"
date: 2022-05-11T19:08:33+08:00
# post thumb
images:
- "images/leetcode/19-remove-nth-node-from-end-of-list.png"
# author
author: "郁金香啊"
# description
description: "Remove Nth Node From End of List"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","中等难度"]
type: "regular"
draft: false
---

给定一个链表，移除这个链表的倒数第n个节点。

## 示例
**示例 1:**
```markdown
输入: head = [1,2,3,4,5], n = 2
输出: [1,2,3,5]
```

**示例 2:**
```markdown
输入: head = [1], n = 1
输出: []
```

**示例 3:**
```markdown
输入: head = [1,2], n = 1
输出: [1]
```

**限制条件:**
* `1 <= 链表长度 <= 30`
* `0 <= 节点值 <= 100`
* `1 <= n <= 链表长度`

## 解决方案
### 解决方案 1 - 双指针
我们假定有快慢两个指针，我们让快指针先走n步，然后再让快慢指针同时向后走，当我们的快指针到达终点的时候，我们的慢指针就刚好处于倒数第n个节点的位置。

当然了此处我们慢指针不能跑到倒数第n个节点的位置，而是应该在倒数第n + 1个节点的位置，否则我们就无法再进行删除操作了。我们怎么做到这样呢，让fast指针先多跑一步就好了。

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode virtualNode = new ListNode(0, head);
        
        ListNode fast = virtualNode;
        ListNode slow = virtualNode;
        
        for(int index = 0;index <= n; index++) {    //注意此处fast指针多走了一步。
            fast = fast.next;
        }
        
        while(fast != null) {
            fast = fast.next;
            slow = slow.next;
        }
        
        slow.next = slow.next.next;
        
        return virtualNode.next;
    }
}
```
