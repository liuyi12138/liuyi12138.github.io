---
title: C++刷Leetcode笔记之 15.3Sum 
date: 2018-08-23 10:50:04
tags: [C++,Leetcode]
categories: 算法
---
**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

Two Sum 的爸爸题，3sum之前一直没做出来，当然这次也没做出来

[Leetcode传送门](https://leetcode.com/problems/3sum/description/)

<!--more--> 

# 3Sum [Difficulty: Medium] 
## 题目
* Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.
* Note: The solution set must not contain duplicate triplets.

* Example:
```
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        
    }
};
```


## 我的思路
* 最直接的想法是3sum = for(Two sum)+去重  这样子，就写出了下面一波代码
* 当然最难的部分还是去重，用算法去重是不可能的，这辈子都不可能用算法去重的，只能调调接口这样子才能勉强维持生活，也是因为如此超时了。。。死在倒数第三个测试用例
```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        for(vector<int>::iterator iter = nums.begin(); iter != nums.end(); iter++) {
            int target = 0 - *iter;
            vector<int>::iterator iternext = iter;
            iternext ++ ;
            for(vector<int>::iterator it = iternext; it != nums.end(); it++) {
                vector<int>::iterator itnext = it;
                itnext ++ ;
                vector<int>::iterator exit = std::find(itnext,nums.end(), target - *it);
                if (exit != nums.end()) {
                    vector<int> temp;
                    temp.push_back(*iter);
                    temp.push_back(*it);
                    temp.push_back(target - *it);
                    sort(temp.begin(),temp.end());
                    result.push_back(temp);
                }
            }

        }
        sort(result.begin(),result.end());
        result.erase(unique(result.begin(), result.end()), result.end());
        return result;
    }
};

```

## 参考思路
* 3Sum有别于 Two sum即在于其要求和为0而不是一个任意值，所以可以考虑正负数的问题
* 我们用两个指针分别指向外层循环选择的数字之后开始的数组首尾两个数，如果两个数和正好为target，则将这三个数一起存入结果中
* 采用类似于快排的方法防止重复及寻找两个数：两个指针都需要检测重复数字。如果两数之和小于target，则我们将左边那个指针i右移一位，使得指向的数字增大一些。同理，如果两数之和大于target，则我们将右边那个指针j左移一位，使得指向的数字减小一些
```C++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        for (int k = 0; k < nums.size(); ++k) {
            if (nums[k] > 0) break;
            if (k > 0 && nums[k] == nums[k - 1]) continue;
            int target = 0 - nums[k];
            int i = k + 1, j = nums.size() - 1;
            while (i < j) {
                if (nums[i] + nums[j] == target) {
                    res.push_back({nums[k], nums[i], nums[j]});
                    while (i < j && nums[i] == nums[i + 1]) ++i;
                    while (i < j && nums[j] == nums[j - 1]) --j;
                    ++i; --j;
                } else if (nums[i] + nums[j] < target) ++i;
                else --j;
            }
        }
        return res;
    }
};
```

## 感想
**果然还是不能被做过的题的思维所限制啊**