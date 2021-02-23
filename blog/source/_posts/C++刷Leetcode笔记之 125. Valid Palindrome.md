---
title: C++刷Leetcode笔记之 125. Valid Palindrome
date: 2018-08-25 21:07:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

回文是比较简单而经典的一道题，但调了很久的bug，写起来有点像3sum的内部循环

[Leetcode传送门](https://leetcode.com/problems/valid-palindrome/description/)

<!--more--> 

# Valid Palindrome [Difficulty Easy] 
## 题目
* Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

* Note: For the purpose of this problem, we define empty string as valid palindrome.
* Example:
```
Input: "A man, a plan, a canal: Panama"
Output: true

Input: "race a car"
Output: false
```

```c++
class Solution {
public:
    bool isPalindrome(string s) {

    }
};
```
## 我的思路
* 就简单的双向循环，又一次beat 100%的简单题，要点就是判断一个字符是否为字母或数字，好像还可以用C++的某方法判读来着
```c++
class Solution {
public:
    bool isPalindrome(string s) {
        if (s.empty()) return true;
        transform(s.begin(),s.end(),s.begin(),::tolower);
        int begin = 0;
        int end = s.size()-1;
        while(begin < end) {
            while (!((s[begin]>='a' && s[begin]<='z') || (s[begin]>='0' && s[begin]<='9')) && begin<end)
                begin++;
            while (!((s[end]>='a' && s[end]<='z') || (s[end]>='0' && s[end]<='9')) && begin<end)
                end--;
            // printf("%c %c\n",s[begin],s[end]);
            if (s[begin] != s[end])
                return false;
            begin++;
            end--;
        }
        return true;
    }
};
```

