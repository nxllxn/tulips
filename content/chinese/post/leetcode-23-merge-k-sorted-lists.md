---
title: "Merge k Sorted Lists"
date: 2022-05-17T19:15:33+08:00
# post thumb
images:
- "images/leetcode/23-merge-k-sorted-lists.png"
# author
author: "郁金香啊"
# description
description: "Merge k Sorted Lists"
# Taxonomies
categories: ["LeetCode","算法"]
tags: ["Java","LeetCode","算法","困难难度"]
type: "regular"
draft: false
---
给定n个按照升序排序的链表，将这n个链表合并成一个升序排序的链表并返回。

## 示例
**示例 1:**
```markdown
输入: lists = [[1,4,5],[1,3,4],[2,6]]
输出: [1,1,2,3,4,4,5,6]
解释: 给定的三个链表是[1->4->5, 1->3->4, 2->6]合并之后的结果是1->1->2->3->4->4->5->6
```

**示例 2:**
```markdown
输入: lists = []
输出: []
```

**示例 3:**
```markdown
输入: lists = [[]]
输出: []
```

**限制条件:**
* k == lists.length
* 0 <= k <= 104
* 0 <= lists[i].length <= 500
* -104 <= lists[i][j] <= 104
* lists[i] is sorted in ascending order.
* The sum of lists[i].length will not exceed 104.

## 解决方案
### 解决方案 1 - 归并
遇到这种已经有序然后合并成一个大的有序列表的题目，首先想到的就是归并。原理很简单，每个回合先两两合并，执行log(n)个回合就可以得到最终的结果了。

此处我们每一次都把后半部分合并到前半部分上面，什么意思呢。比如有八个链表，第一回合，我们合并时
* 7 + 3 -> 3
* 6 + 2 -> 2
* 5 + 1 -> 1
* 4 + 0 -> 0

然后第二个回合
* 3 + 1 -> 1
* 2 + 0 -> 0

第三个回合
* 1 + 0 -> 0

执行三个回合之后，第一个链表就是我们想要的结果。当然了实际编码的的时候我们还需要考虑奇数长度的情况。不过也不是很复杂。让我们看下代码实现。

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
    public ListNode mergeKLists(ListNode[] lists) {
        if(lists.length == 0) {
            return null;
        }
        
        ListNode[] temp = lists;
        ListNode[] res = lists;
        while(temp.length > 1) {
            res = new ListNode[(temp.length + 1) >> 1];
            
            int index = 0;
            for(;index < temp.length / 2;index++) {
                res[index] = merge(temp[index],temp[temp.length - 1 - index]);
            }
            
            if((temp.length & 1) == 1) {
                res[index] = temp[index];
            }
            
            temp = res;
        }
        
        return res[0];
    }
    
    private ListNode merge(ListNode first, ListNode second) {
        ListNode virtual = new ListNode(0);
        ListNode current = virtual;
        while(first != null && second != null) {
            if(first.val <= second.val) {
                current.next = new ListNode(first.val);
            
                current = current.next;
                
                first = first.next;
            } else {
                current.next = new ListNode(second.val);
            
                current = current.next;
                
                second = second.next;
            }
        }
        
        if(first != null) {
            current.next = first;
        }
        
        if(second != null) {
            current.next = second;
        }
        
        return virtual.next;
    }
}
```