---
title: C++刷Leetcode笔记之 1.Two Sum 
date: 2018-08-20 15:46:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

而Two Sum 是Leetcode的第一题，大一的时候也刷过，就先拿它来练手吧

[Leetcode传送门](https://leetcode.com/problems/two-sum/description/)

<!--more--> 

# Two Sum [Difficulty: Easy]
## 题目
* Given an array of integers, return indices of the two numbers such that they add up to a specific target.
* You may assume that each input would have exactly one solution, and you may not use the same element twice.

* Example: Given nums = [2, 7, 11, 15], target = 9,
* Because nums[0] + nums[1] = 2 + 7 = 9,
* return [0, 1].

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        
    }
};
```
## 我的思路
### 1.最简单的思路就是两个for循环，比较简单，时间复杂度高 ，代码略

### 2.进一步的思想既是一个for循环然后想用二分法解决问题
* 1)排序问题，想使用二分法则必须先排序，在此使用c++中的sort函数对vector进行排序
* 2)下标还原问题，由于打乱了原vector的顺序，故需要先保存一份原顺序，然后使用find函数查找得到的value在原vector中的位置
* 3)元素重复问题，若有原vector中有两个相同的元素，则下标还原时会取到两个相同的下标，故需要在找到第一个元素的下标时修改该元素的值
* 由于代码过于复杂且写出的代码bug太多了，故代码略

### 3.采用分治法解决问题
* 此思路来自[分治法解决最大子数组问题的解法](https://www.cnblogs.com/AlgrithmsRookie/p/5882379.html)
* 将原vector排序后(从小到大)分为两部分，则需要寻找的两个数字的分布有三种可能:
    * 1.都在左侧(小)
    * 2.都在右侧(大)
    * 3.左右两边各一个
* 都在左侧或都在右侧则将一个大问题转换为一个更小规格的同类问题
* 则只需要通过循环在左右两边各找出一个数即可
* 时间复杂度最高为n^2/4,但代码过于复杂，暂未实现
* 2020/1/30 使用C实现了分治法，由于还是需要部分遍历，并不比直接遍历快多少。
```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */

int ergodic(int* nums, int l, int h, int target, int* result){
    int med = (l + h) / 2;
    for(int i = l; i <= med; ++i){
        for(int j = med + 1; j <= h; ++j){
            if(target == nums[i] + nums[j]){
                result[0] = i;
                result[1] = j;
                return 1;
            }
        }
    }
    return 0;
}

int find2Sum(int* nums, int l, int h, int target, int* result){
    if(h <= l)
        return 0;
    if(h == l + 1){
        if(target == nums[l] + nums[h]){
            result[0] = l;
            result[1] = h;
            return 1;
        }
        return 0;
    }
    int med = (l + h) / 2;
    int leftRes = find2Sum(nums, l, med, target, result);
    int rightRes = find2Sum(nums, med, h, target, result);
    if(!leftRes && !rightRes)
        return ergodic(nums, l, h, target, result);
    else
        return 1;
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    *returnSize = 0;
    if(numsSize <= 1) return NULL;
    int* result = (int*)malloc(2*sizeof(int));
    if(find2Sum(nums, 0, numsSize-1, target, result)){
        *returnSize = 2;
    }
    else{
        *returnSize = 0;
    }
    return result;
}


```

## 参考思路
### 利用find函数搜索，省去一次for循环
* 1)存储问题，由于不能通过value搜索key则应该以下标为value
* 2)重复问题，若有多个相同的元素则key会重复，此时考虑用multimap(可存储重复key的map)
```c++
class Solution {
public:  
    vector<int> twoSum(vector<int> &numbers, int target) {
        multimap <int,int> nums;
        vector<int> result;
        multimap<int,int>::iterator it;
        for(int i = 0; i < numbers.size(); i++)
            nums.insert(pair<int, int>(numbers[i], i));
        for (it=nums.begin(); it!=nums.end(); ++it) {
            if (nums.find(target - it->first) != nums.end() && nums.find(target - it->first)->second != it->second) {
                result.push_back(it->second);
                result.push_back(nums.find(target - it->first)->second);
                sort(result.begin(),result.end());
                return result;
            }
        }
        return result;
    }
};

```
## 感想
**果然轮子才是第一生产力，一个multimap解决了n多问题。。。不过我对容器的把我能力确实还不够**
