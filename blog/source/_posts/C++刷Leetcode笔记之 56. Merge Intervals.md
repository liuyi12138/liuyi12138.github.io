---
title: C++刷Leetcode笔记之 56. Merge Intervals
date: 2018-08-27 20:56:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

貌似刷题指南上的难度为Hard，就留到了后面，然后五次提交就AC了，还以为自己变强了23333

[Leetcode传送门](https://leetcode.com/problems/merge-intervals/description/)

<!--more--> 

# Merge Intervals [Difficulty: Medium]
## 题目
* Given a collection of intervals, merge all overlapping intervals.
```
Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].


Input: [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considerred overlapping.
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
    vector<Interval> merge(vector<Interval>& intervals) {

    }
};
```
## 我的思路
* 虽然范例里面是正序的，第一反应还是排序，果然是警觉了不少呢
* 然后主要就是遍历，然后比对了，没什么特别之处，就是最后结果不算很好
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
    vector<Interval> merge(vector<Interval>& intervals) {
        int max = intervals.size();
        if (max <= 1) return intervals;
        sort(intervals.begin(),intervals.end(),sortFunction);
        vector<Interval>::iterator iter,iterNext;
        iter = intervals.begin();
        iterNext = iter;
        iterNext++;
        while (iterNext != intervals.end()) {
            if (iter->end >= iterNext->start) {
                iter->end = iterNext->end > iter->end ? iterNext->end : iter->end;
                intervals.erase(iterNext);
            }
            else {
                iter++;
                iterNext++;
            }
        }
        return intervals; 
    }
    
    static bool sortFunction(Interval& interval1 ,Interval& interval2) {
        return (interval1.start < interval2.start);
    }
};
```

## 参考思路
* 差别在于我用了迭代器，而别人的循环就比较轻便，而且少了删除元素的操作
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
    
    static bool compare(Interval a, Interval b) {
        return a.start < b.start;
    }
    
    vector<Interval> merge(vector<Interval>& intervals) {
        if(intervals.size() < 2)
            return intervals;
        sort(intervals.begin(), intervals.end(), compare);
        vector<Interval> result;
        int start = intervals[0].start, end = intervals[0].end;
        for(int i=1; i<intervals.size(); i++) {
            if(intervals[i].start > end) {
                result.push_back(Interval(start, end));
                start = intervals[i].start;
                end = intervals[i].end;
            } else
                end = max(intervals[i].end, end);
        }
        result.push_back(Interval(start, end));
        return result;
    }
};

```
