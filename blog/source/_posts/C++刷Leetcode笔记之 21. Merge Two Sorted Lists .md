---
title: C++刷Leetcode笔记之 21. Merge Two Sorted Lists
date: 2018-08-23 22:42:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

遇到过的最简单的一个题23333

[Leetcode传送门](https://leetcode.com/problems/merge-two-sorted-lists/description/)

<!--more--> 

# Merge Two Sorted Lists [Difficulty Easy] 
## 题目
* Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

* Example:
```
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        
    }
};
```
## 我的思路
* 这个思路就很清晰了，逐个比对添加就好了
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (l1 == NULL)
            return l2;
        else if(l2 == NULL)
            return l1;
        ListNode *p = l1;
        ListNode *q = l2;
        ListNode *head;
        ListNode *l3;
        if (p->val <= q->val) {
            l3 = p;
            p = p->next;
        }
        else {
            l3 = q;
            q = q->next;
        }
        head = l3;
        while (p != NULL && q != NULL) {
            if (p->val > q->val) {
                l3->next = q;
                l3 = q;
                q = q->next;
            }
            else {
                l3->next = p;
                l3 = p;
                p = p->next;
                }
        }
        l3->next = (p == NULL) ? q : p;
        return head;
    }
};
```

## 参考思路
* 可以优化的位置就是最开始的那次判断可以去掉，让l3 = ListNode(0),然后最后返回head->next就好了现场(这个next很精妙啊)
