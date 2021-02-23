---
title: C++刷Leetcode笔记之 20.Valid Parentheses
date: 2018-08-23 18:05:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

[Leetcode传送门](https://leetcode.com/problems/valid-parentheses/description/)

<!--more--> 

# Valid Parentheses [Difficulty Easy] 
## 题目
* Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

* An input string is valid if:

* Open brackets must be closed by the same type of brackets.
* Open brackets must be closed in the correct order.
* Note that an empty string is also considered valid.

Example：
```
Input: "()[]{}"
Output: true

Input: "([)]"
Output: false
```

```c++
class Solution {
public:
    bool isValid(string s) {
        
    }
};

```

## 我的思路
* 每次在给定字符串中截取一段括号，当正括号和反括号数量相同时进行检测(使用自定义的check函数)，check函数检测给定段的字符串是否正确
* check函数每次检测给定字符串最外层的括号是否匹配，然后去除匹配的括号，截取剩下的字符串传入isValid函数检测
* 两个函数递归造成时间较长
```c++
class Solution {
public:
    bool isValid(string s) {
        int size = s.size();
        if (size == 0) return true;
        if (size%2 == 1) return false;
        if ((s[size-1] != ')') && (s[size-1] != ']') && (s[size-1] != '}')) return false;
        else {
            int ref = 0,suf = 0;
            int begin = 0,flag=0;
            for (int i = 0; i<size; ++i) {
                if ((s[begin] != '(') && (s[begin] != '[') && (s[begin] != '{')) 
                    return false;
                if ((s[i] == '(') || (s[i] == '[') || (s[i] == '{'))
                    ref++;
                else
                    suf ++;
                if (ref == suf) {
                    if (!check(s,begin,i))
                        return false;
                    else {
                        flag = 1;
                        begin = i+1;
                    }
                }
            }
            if (!flag)
                return false;
            return true;
        }
    }
    
    bool check(string s,int begin,int end) {
        if ((s[begin]=='('&&s[end]==')')||(s[begin]=='['&&s[end]==']')||(s[begin]=='{'&&s[end]=='}')) {
            if (begin < end -1) {
                if (isValid(s.substr(begin+1,end-begin-1)))
                    return true;
                return false;
            }
            return true;
        }
        return false;
    }
};

```


## 参考思路
* 此问题可参考[逆波兰表达式](https://blog.csdn.net/uestclr/article/details/50630906)
* 对于运算符匹配的问题都可以使用栈来解决
```c++
class Solution 
{
public:
    stack<char> stk;
    bool isValid(string s) 
    {
        for(int i=0; i<s.size();i++)
        {
            if( s[i]=='(' || s[i]=='[' || s[i]=='{' )
            {
                stk.push(s[i]);
            }
            if( s[i]==')' || s[i]==']' || s[i]=='}' )
            {
                if (stk.empty())
                    return false;
                else
                {   
                if (s[i]==')' && stk.top()=='(' )
                    stk.pop();
                else if (s[i]==']' && stk.top()=='[' )
                    stk.pop();
                else if (s[i]=='}' && stk.top()=='{' )
                    stk.pop();
                else
                    return false;
                }
            }
        }
        
        return stk.empty();
    }
};
```

## 感想
* 感觉对数据结构的掌握还是不够，一般不会想到用栈，队列，堆等数据结构来解决问题