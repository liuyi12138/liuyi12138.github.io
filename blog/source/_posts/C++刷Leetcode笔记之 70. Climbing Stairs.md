---
title: C++刷Leetcode笔记之 70. Climbing Stairs
date: 2018-08-24 15:30:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

递归和动规的典型问题，也是迅速ac了

[Leetcode传送门](https://leetcode.com/problems/climbing-stairs/description/)

<!--more--> 

# Climbing Stairs [Difficulty: Easy] 
## 题目
* You are climbing a stair case. It takes n steps to reach to the top.

* Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

* Note: Given n will be a positive integer.
* Example:
```
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps

Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

```c++
class Solution {
public:
    int climbStairs(int n) {
        
    }
};
```
## 我的思路
* 第一想法是递归，写出第一版代码如下
```c++
class Solution {
public:
    int climbStairs(int n) {
        if (n == 1) return 1;
        if (n == 2) return 2;
        return climbStairs(n-1) + climbStairs(n-2);
    }
};
```
* 显然这代码超时了，因为递归过程中很多步骤会被重复计算2次，所以最好是把中间值存起来，这时候动规的优势就体现出来了

```c++
class Solution {
public:
    int climbStairs(int n) {
        if (n == 1) return 1;
        if (n == 2) return 2;
        int results[n+1];
        results[1] = 1;
        results[2] = 2;
        for (int i=3; i<=n; ++i)
            results[i] = results[i-1] + results[i-2];
        return results[n] ;
    }
};
```

