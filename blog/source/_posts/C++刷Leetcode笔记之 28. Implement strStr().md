---
title: C++刷Leetcode笔记之 28. Implement strStr()
date: 2018-08-24 15:15:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

更简单的出现了

[Leetcode传送门](https://leetcode.com/problems/implement-strstr/description/)

<!--more--> 

# Implement strStr() [Difficulty Easy] 
## 题目
* Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.
* Example:
```
Input: haystack = "hello", needle = "ll"
Output: 2

Input: haystack = "aaaaa", needle = "bba"
Output: -1
```

```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
    }
};
```
## 我的思路
* 本来想用KMP算法，但发现这方面的算法可不少，不如老老实实用轮子
* [各种字符串匹配算法](https://blog.csdn.net/u011467044/article/details/55008649)
```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle == "") return 0;
        return haystack.find(needle);;  
    }
};
```

