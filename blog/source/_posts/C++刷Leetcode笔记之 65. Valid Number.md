---
title: C++刷Leetcode笔记之 65. Valid Number
date: 2018-08-29 20:56:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

又AC的第一个HARD题，hhhh

[Leetcode传送门](https://leetcode.com/problems/valid-number/description/)

<!--more--> 

# Valid Number [Difficulty: Hard] 
## 题目
* Validate if a given string is numeric.

* Some examples:
    * "0" => true
    * " 0.1 " => true
    * "abc" => false
    * "1 a" => false
    * "2e10" => true

* Note: It is intended for the problem statement to be ambiguous. You should gather all requirements up front before implementing one.
```c++
class Solution {
public:
    bool isNumber(string s) {

};
```
## 我的思路
* 思路都在注释里，做这个题的时候心里是mmp的
```c++
class Solution {
public:
    bool isNumber(string s) {
        int max = s.size();
        int flag = 0; //判断小数点是否存在
        int numExitRef = 0; //判断小数点前是否有数字
        int unmExitNext = 0; //判断小数点后是否有数字
        int numEnd = 0; //判断是否到数字结尾
        int eExit = 0; //判断e是否存在
        int eNumRef = 0; //判断e前面是否有数字
        int eNumNext = 0; //判断e后面是否有数字
        int symbol = 0; //判断是否有符号
        for (int i = 0; i<max; ++i) {
            if (!symbol && !numExitRef && !flag && s[i] == ' ') continue; //判断是否为数字开始前的空格
            else if (s[i] == '-' || s[i] == '+') {
                if (flag || numExitRef || symbol) //如果有小数点，前面存在数字，已存在符号则判为false
                    return false;
                else
                    symbol = 1; //如果没有上述情况，则判定已有符号
            }
            else if (s[i] == '.' && !flag && !eExit) { //如果此字符为小数点且前面没有小数点而且e不存在
                if (numEnd) //如过到数字结尾了，则判false
                    return false;
                flag = 1; //判定已有小数点
            }
            else if (eNumRef && s[i] == 'e' && !eExit) { //如果e前面有数字且e不存在
                eExit = 1; //判定e存在
                unmExitNext = 0; //由于e后和e前可以当做两个数字，则将这些值都置0
                numExitRef = 0;
                symbol = 0;
                flag = 0;
            }
            else if (s[i] < '0' || s[i] > '9') { //如果此字符不为以上特殊符号和数字
                if (s[i] == ' ') //如果此字符为空
                    numEnd = 1; //判定已到数字结尾
                else
                    return false; //否则判为false
            }
            else { //如果此字符为数字
                if (flag) unmExitNext = 1; //判定是小数点前还是小数点后
                else numExitRef = 1;
                
                if (eExit) eNumNext = 1; //判定是e前还是e后
                else eNumRef = 1;
                    
                if (numEnd) //如果已经到数字末尾了则判为false
                    return false;
            }
        }
        if (eExit && !eNumNext) return false; //如果e存在且e后没有数字，判为false
        else if (numExitRef || unmExitNext) return true; //如果小数点前后有数字，判为true
        return false;
    }
};
```