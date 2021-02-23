---
title: C语言刷Leetcode笔记之 88. Merge Sorted Array
date: 2020-02-01 14:33:04
tags: [C语言,Leetcode]
categories: 算法
---

**本来说今天选一个简单题做来着，结果想着想着这奇奇怪怪的方法他就出来了。**

**ps:以后打死我也不会再研究一些奇奇怪怪的算法了，人生苦短我用qsort**

[Leetcode传送门](https://leetcode-cn.com/problems/merge-sorted-array/)

<!--more--> 

## 思路分析
**其实主要的思想都是一样的(毕竟题很简单),但思路拓宽来，就扯到归并排序了，然后发现了奇奇怪怪的原地归并排序(谁想出来的烧脑玩意)**

### 暴力qsort
* 做题第一步，必定先暴力一次过，qsort给劲，效果也还不错

```c
int compare(void* a, void*b){
    return *(int*)a - *(int*)b;
}

void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size, int n){
    for(int i = 0; i < n; ++i){
        nums1[m+i] = nums2[i];
    }
    qsort(nums1, m+n, sizeof(int), compare);
}
```

### 双指针比较法
* 写这个方法前是有很难受的思考与实践的，是真的难受
* 第一想法是还是先将num2搬到num1中去，由于合并后的数组具有特定规律(前后排序完成)，可采用特定方法进行排序
* 有意思的是这时的情形与归并排序的merge相同
* 然而归并排序的merge需要用到额外空间，这样必定浪费时间和空间
* 所以既然要用归并排序的合并思想，那不如直接用num2的空间，采用双指针比较法来合并。
* 思想大概是每次选出一个最大的数放在最后面

```c
void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size, int n){
    int i = 0;
    int j = 0;
    int count = 1;
    while(i < m && j < n){
        if(nums1[m-1-i] > nums2[n-1-j]){
            nums1[m+n-count] = nums1[m-1-i];
            i++;
        }
        else{
            nums1[m+n-count] = nums2[n-1-j];
            j++;
        }
        count++;
    }
    while(j < n){
        nums1[m+n-count] = nums2[n-1-j];
        j++;
        count++;
    }
}
```

### 原地归并排序Merge
* 左思右想，有没有法子把num2合到num1中后，不开辟空间把俩数组合并呢
* 自己尝试了一下并不顺利
* 于是百度了不消耗空间的归并函数，于是乎发现了原地归并排序这么个玩意
* 想要具体了解可以参考[原地归并排序](https://www.cnblogs.com/xiaorenwu702/p/5880841.html)
* 但有一说一,最终效果并不咋地,毕竟不耗空间是以时间复杂度为代价的
* 其中一个有意思的思想就是把通过三次数组倒置的方式来实现数组旋转，有兴趣可以尝试做一下[Leetcode T189 Rotate Array](https://leetcode-cn.com/problems/rotate-array/)

```c
void reverse(int* nums,int start, int end)
{
    int i = start;
    int j = end - 1;
    int temp;
    while (i<j)
    {
        temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
        i++;
        j--;
    }
}

void exchange(int* nums,int start, int mid, int end)
{
    reverse(nums, start, mid);
    reverse(nums, mid, end);
    reverse(nums, start, end);
}

void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size, int n){
    for(int i = 0; i < n; ++i){
        nums1[m+i] = nums2[i];
    }

    int i = 0;
    int j = m;
    int mid = m;
    while (i < j && j < m+n)
    {
        int step=0;
        while (i < j && nums1[i] <= nums1[j])
            i++;
        while (j < m+n && nums1[j] < nums1[i])
        {
            j++;
            step++;
        }
        exchange(nums1,i,mid,j);
        i += step;
        mid = j;
    }
}


```

## More
* 搞了半天其实也没有提高算法的效率，只能说是拓展了思路(以后打死我也不会用这个算法了)
* 康康排行榜上这题的最佳解法居然是写了个快排
* 我就不明白了为啥自己写的快排能快过qsort...
* 代码我就放这了，有兴趣自己康

```c
void merge(int* nums1, int nums1Size, int m, int* nums2, int nums2Size, int n){
    int i=0,j=0;
    for(i=0;i<n;i++){
        nums1[m+i]=nums2[i];
    }
    Q_sort(nums1,0,m+n-1);
    return nums1;
}
void Q_sort(int *array,int low,int high){
    if(low>=high)
        return;
    int key=array[low];
    int start=low,end=high;
    while(start<end){
        while(start<end&&key<=array[end])end--;
        array[start]=array[end];
        while(start<end&&key>=array[start])start++;
        array[end]=array[start];
    }
    array[start]=key;
    Q_sort(array,low,start-1);
    Q_sort(array,start+1,high);
}
```