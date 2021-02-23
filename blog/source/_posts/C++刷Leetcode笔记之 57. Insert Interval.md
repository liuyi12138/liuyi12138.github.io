---
title: C++刷Leetcode笔记之 57. Insert Interval
date: 2018-08-29 17:56:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

AC的第一个HARD题，感觉还没有127难23333，可能是做了56的原因吧

[Leetcode传送门](https://leetcode.com/problems/insert-interval/description/)

<!--more--> 

# Insert Interval [Difficulty: Hard] 
## 题目
* Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

* You may assume that the intervals were initially sorted according to their start times.
```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]

Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```
```c++
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    vector<Interval> insert(vector<Interval>& intervals, Interval newInterval) {

};
```
## 我的思路
### 1.insert+56
* 第一次AC就是直接insert一下,然后就把56的代码拿上去了23333
### 2.选择性insert+56
* 避免了sort，选择性insert，通过判断将其insert到正确的位置，顺便从插入位置开始循环，减少循环次数
```c++
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    vector<Interval> insert(vector<Interval>& intervals, Interval newInterval) {
        int max = intervals.size();
        if (max == 0) {
            intervals.push_back(newInterval);
            return intervals;
        }
        int flag = 0;
        if (newInterval.start <= intervals[0].start) {
            intervals.insert(intervals.begin(), 1, newInterval);
            flag = 1;
        }
        if (newInterval.start > intervals[max-1].start) {
            intervals.push_back(newInterval);
            flag = 1;
        }
        vector<Interval> result;
        for (int i = 0; i<max; ++i) {
            if (flag || (intervals[i].start < newInterval.start && intervals[i+1].start >= newInterval.start)) {
                if (!flag)
                    intervals.insert(intervals.begin()+i+1, 1, newInterval);
                int mstart = intervals[i].start, mend = intervals[i].end;
                for (int j = i+1; j<max+1; ++j) {
                    if(intervals[j].start > mend) {
                        result.push_back(Interval(mstart, mend));
                        mstart = intervals[j].start;
                        mend = intervals[j].end;
                    }
                    else
                        mend = intervals[j].end > mend ? intervals[j].end : mend;
                }
                result.push_back(Interval(mstart, mend));
                return result;
            }
            result.push_back(intervals[i]);
        }
    }
};
```
