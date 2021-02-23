---
title: C++刷Leetcode笔记之 88. Merge Sorted Array
date: 2018-08-24 21:46:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

和21.Merge Two Sorted Lists就很像，ac速度也很像

[Leetcode传送门](https://blog.csdn.net/lnho2015/article/details/50962989)

<!--more--> 

# Merge Sorted Array [Difficulty Easy] 
## 题目
* Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

* Note:
    * The number of elements initialized in nums1 and nums2 are m and n respectively.
    * You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2.

```
Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```
```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {

    }
};
```
## 我的思路
* 很平常的一个题，突发奇想就用栈来做了，效果还不错，感觉比标答好
```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        printf("%d\n\n",nums1.size());
        stack<int> stk1;
        stack<int> stk2;
        for (int i = m-1; i>=0; --i)
            stk1.push(nums1[i]);
        for (int j = n-1; j>=0; --j)
            stk2.push(nums2[j]);    
        
        int count = 0;
        while (!stk1.empty() && !stk2.empty()) {
            // printf("1 %d\n",nums1[count]);
            if (stk1.top() < stk2.top()) {
                nums1[count] = stk1.top();
                stk1.pop();
            }
            else {
                nums1[count] = stk2.top();
                stk2.pop();
            }
            count++;
        }
        while (!stk1.empty()) {
            // printf("2 %d\n",nums1[count]);
            nums1[count] = stk1.top();
            stk1.pop();
            count++;
        }
        while (!stk2.empty()) {
            // printf("3 %d\n",nums1[count]);
            nums1[count] = stk2.top();
            stk2.pop();
            count++;
        }
        return;
    }
};
```
