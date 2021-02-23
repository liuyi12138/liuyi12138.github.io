---
title: C++刷Leetcode笔记之 73. Set Matrix Zeroes
date: 2018-08-24 18:24:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

把空间复杂度看成时间复杂度可把人折磨坏了

[Leetcode传送门](https://leetcode.com/problems/set-matrix-zeroes/description/)

<!--more--> 

# Set Matrix Zeroes [Difficulty: Medium] 
## 题目
* Given a m x n matrix, if an element is 0, set its entire row and column to 0. Do it in-place.
* Example:
```
Input: 
[
  [1,1,1],
  [1,0,1],
  [1,1,1]
]
Output: 
[
  [1,0,1],
  [0,0,0],
  [1,0,1]
]
```
```
Input: 
[
  [0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]
]
Output: 
[
  [0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]
]
```

* Follow up:

* A straight forward solution using O(mn) space is probably a bad idea.
* A simple improvement uses O(m + n) space, but still not the best solution.
* Could you devise a constant space solution?

```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        
    }
};
```

## 我的思路
* 把空间复杂度看成时间复杂度，然后想了很久，结果还是两个for循环
* 用了multimap存储0的信息，value为1时key是行数，value为0时key是列数
```c++
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        multimap<int,int> Zeros;
        int rowSize = matrix.size();
        int colSize = matrix[0].size();
        for (int i = 0; i<rowSize; ++i)
            for (int j = 0 ;j<colSize; ++j)
                if (matrix[i][j] == 0){
                    Zeros.insert(pair<int, int>(i, 1));
                    Zeros.insert(pair<int, int>(j, 0));
                }
        
        multimap<int,int>::iterator iter = Zeros.begin();
        for (; iter!=Zeros.end(); ++iter) {
            if (iter->second == 1) {
                for (int m =0; m<colSize; ++m) {
                    matrix[iter->first][m] = 0;
                }
            }
            else {
                for (int n =0; n<rowSize; ++n) {
                    matrix[n][iter->first] = 0;
                }
            }
        }
        return;
    } 
};
```

## 参考思路
* 答案用的是两个list存的信息，而不是multimap，确实看不出来为什么比我快，口亨


