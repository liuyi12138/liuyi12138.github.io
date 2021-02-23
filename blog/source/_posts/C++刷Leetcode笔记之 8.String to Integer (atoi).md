---
title: C++刷Leetcode笔记之 8.String to Integer (atoi)
date: 2018-08-21 11:37:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

[Leetcode传送门](https://leetcode.com/problems/string-to-integer-atoi/description/)

<!--more--> 

# String to Integer (atoi) [Difficulty: Easy]
## 题目
* Implement atoi which converts a string to an integer.

* The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

* The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

* If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

* If no valid conversion could be performed, a zero value is returned.

* Note:Only the space character ' ' is considered as whitespace character.
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−2^31,  2^31 − 1]. If the numerical value is out of the range of representable values, INT_MAX (2^31 − 1) or INT_MIN (−2^31) is returned.

```c++
class Solution {
public:
    int myAtoi(string str) {
        
    }
};
```
## 我的思路
思路也什么好说的，难点是判断该数是否越界
```c++
class Solution {
public:
    int myAtoi(string str) {
        int result = 0;
        int max = str.size();
        int flag = 0; //判断正负
        int exit = 0; //判断'+''-'是否存在
        int num = 0; //判断前面是否出现数字
        for (int i = 0; i<max; ++i) {
            if (str[i] == '-' && !num) {
                if (exit)
                    return 0;
                flag = 1;
                exit = 1;
            }
            else if (str[i] == '+'&& !num) {
                if (exit)
                    return 0;
                exit = 1;
            }
            else if (str[i] == ' '&& !num) {
                if (exit)
                    return 0;
            }
            else if (str[i]<'0' || str[i] >'9') {
                break;
            }
            else {
                num = 1;
                if (result > (INT_MAX-(int)(str[i] - '0'))/10) {
                    if (flag)
                        return INT_MIN;
                    else 
                        return INT_MAX;
                }
                result = result*10 + (int)(str[i] - '0');
                printf("%s\n",&str[i]);
                printf("%d\n",result);
            }
        }
        if (flag)
            result = 0 - result;
        return result;
    }
};
```

## 参考思路
* 比起我的代码，别人的代码优点在于:
    * 逻辑清晰，我的代码标志符过杂乱
    * 使用了long long，不用处理溢出的问题
```c++
class Solution {
public:
    int myAtoi(string str) {
        long long sum = 0;
        int base=10;
        int n = str.size();
        bool flag = true; //判断是否出现有效字符
        int sign = 1; //判断正负
        for(int i = 0; i < n; i++)
        {
            if(isalpha(str[i]))
            {
                return sum;
            }
            else if(str[i]==' '&&flag)
            {
                continue;
            }
            else if(str[i]>='0' && str[i]<='9')
            {
                sum = 10*sum + sign*(str[i] - '0');
                flag = false;
            }
            else if(str[i] == '-' &&flag)
            {
                sign = -1;
                flag = false;
            }
            else if(str[i] == '+' && flag)
            {
                flag = false;
            }
            else return sum;
            if(sum > INT_MAX) return INT_MAX;
            else if(sum < INT_MIN) return INT_MIN;
        }
        return sum;
    }
};
```
## 感想
**感觉这种题还是在考验对异常情况的处理，由于一开始没有全面的思考，其实代码是在一点点修修补补拼凑起来的，很多东西都没有考虑到**