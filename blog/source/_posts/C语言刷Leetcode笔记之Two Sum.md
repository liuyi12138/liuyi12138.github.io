---
title: C语言刷Leetcode笔记之 1. Two Sum
date: 2020-01-31 15:50:04
tags: [C语言,Leetcode]
categories: 算法
---

**2020寒假在家，给19级小朋友出题时想了Two Sum的多种实现方法如下。**

[Leetcode传送门](https://leetcode-cn.com/problems/two-sum/)

<!--more--> 

## 思路分析
**主要想到了三种思路及其改进，以及还有最近刷题常见的Hash表法**

### 暴力遍历
* 最直观的方法就是遍历了
* 时间复杂度O(n^2) 空间复杂度O(1)

```c
int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    int* result = (int*)malloc(2*sizeof(int));
    for(int i = 0; i < numsSize; ++i){
        for(int j = i + 1; j < numsSize; ++j){
            if(nums[i] + nums[j] == target){
                result[0] = i;
                result[1] = j;
                *returnSize = 2;
                return result;
            }
        }
    }
    *returnSize = 0;
    return result;
}
```

### 分治法
* 这个想法由来已久，去年用C++刷题时就想到能否尝试，主要是看到算法导论上有一个例题的分析方法与之类似一直想试一下，直到今天才have a try 但改进效果有限
* ([去年的C++刷题笔记](https://liuyi12138.github.io/2018/08/20/C%E5%88%B7Leetcode%E7%AC%94%E8%AE%B0%E4%B9%8B1TwoSum/))
* 时间复杂度O(n^2) 空间复杂度O(1)

```c
/*
    分治法实现2Sum，思路为把数组分为两半，问题变为3个子问题：
    2个数都在左侧/2个数都在右侧/左右各一个
    如果两个数在同侧则继续递归,最小子问题为只剩两个数时直接比较，只剩一个数时判false
    如果两个数分别在两边，则for循环遍历两侧，时间复杂度为O(n^2),但系数为原来的1/4
    此方法时间复杂度为O(n^2),最理想条件下时间复杂度为O(nlogn),空间复杂度为O(1)
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

### 先排序后搜索
* 思路为排序后搜索，需要开辟空间来存储原数组防止丢失索引

#### 二分搜索
* 排序和搜索的时间复杂度都为O(nlogn),算法的空间复杂度为O(n)
* (这个二分搜索代码的边界条件有问题aaaaa，真的调不出来，代码思路在这，凑合着看)

```c
int compare(void* a, void* b){
    return *(int*)a - *(int*)b;
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    int* temp = (int*)malloc(numsSize*sizeof(int));
    int* result = (int*)malloc(2*sizeof(int));
    int resultl, resulth;
    for(int i = 0; i < numsSize; ++i){
        temp[i] = nums[i];
    }
    qsort(temp, numsSize, sizeof(int), compare);
    int flag = 0;
    for(int i = 0; i < numsSize; ++i){
        if(flag) break;
        int l = i;
        int h = numsSize - 1;
        while(l <= h){
            int mid = (l + h) / 2;
            if(temp[i] + temp[mid] == target){
                resultl = temp[i];
                resulth = temp[mid];
                flag = 1;
                break;
            }
            else if(temp[l] + temp[mid] > target) h = mid - 1;
            else l = mid + 1;
        }
    }
    if(flag){
        *returnSize = 2;
        int flagl = 0;
        int flagh = 0;
        for(int i = 0; i < numsSize; ++i){
            if(!flagl && nums[i] == resultl){
                result[0] = i;
                flagl = 1;
            }
            else if(!flagh && nums[i] == resulth){
                result[1] = i;
                flagh = 1;
            }
        }
    }
    else{
        *returnSize = 0;
    }
    return result;
}
```

#### 双指针遍历搜索
* 此方法为上一个思路的改进，改进点在于搜索
* 因为qsort的时间复杂度最差为O(nlogn)，但搜索一定是O(nlogn)所以需要改进
* 具体措施为将二分法改为双指针遍历的方法，时间复杂度为O(n)
* 算法整体的时间复杂度还是O(nlogn),空间复杂度为O(n)

```c
int compare(void* a, void* b){
    return *(int*)a - *(int*)b;
}

int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    int* temp = (int*)malloc(numsSize*sizeof(int));
    int* result = (int*)malloc(2*sizeof(int));
    int resultl, resulth;
    for(int i = 0; i < numsSize; ++i){
        temp[i] = nums[i];
    }
    qsort(temp, numsSize, sizeof(int), compare);
    int flag = 0;
    int l = 0;
    int h = numsSize - 1;
    while(l < h){
        if(temp[l] + temp[h] == target){
            resultl = temp[l];
            resulth = temp[h];
            flag = 1;
            break;
        }
        else if(temp[l] + temp[h] < target) l++;
        else h--;
    }

    if(flag){
        *returnSize = 2;
        int flagl = 0;
        int flagh = 0;
        for(int i = 0; i < numsSize; ++i){
            if(!flagl && nums[i] == resultl){
                result[0] = i;
                flagl = 1;
            }
            else if(!flagh && nums[i] == resulth){
                result[1] = i;
                flagh = 1;
            }
        }
    }
    else{
        *returnSize = 0;
    }
    return result;
}
```

### Hash表法
* Hash表是什么我在此就不讲解了，不懂的自行百度
* 最近刷题得分高的解法里面很大一部分都是使用Hash表去解
* 我不大愿意使用Hash表主要是因为C标准库中本身没有实现Hash表(就是懒)
* 之前也有过用C实现Hash表来着([Hash表的C语言实现](https://github.com/liuyi12138/Utils/tree/master/C_HashSet))
* 言归正传，Hash表法的思路其实很简单，主要还是靠搜索，不过通过Hash的方式实现搜索可以将时间复杂度降低
* Hash存数据时间复杂度为O(1)，取数据时间复杂度为O(1) ~ O(n)
* 整体而言该算法的时间复杂度在O(n) ~ O(n^2)之间，空间复杂度是O(n),个人感觉效果一般
* 最后贴一个嫖来的Hash表法实现2Sum，大家自行体会
```c
/**
 * Note: The returned array must be malloced, assume caller calls free().
 */

struct hash_node {
    struct hash_node *next;
    int index;
    int element;
};

struct hash_node *hash_mem;
struct hash_node **hash_header;
int hash_size = 16;

int hash_func(int num)
{
    if (num < 0) {
        return (0 - (num % hash_size));
    }
    return (num % hash_size);
}

 int build_hash(int *nums, int numsSize)
 {
     int i, v;
     hash_header = malloc(hash_size * sizeof(struct hash_node *));
     if (hash_header == NULL) {
         return -1;
     }
     memset(hash_header, 0, hash_size * sizeof(struct hash_node *));
     hash_mem = malloc(numsSize * sizeof(struct hash_node));
     if (hash_mem == NULL) {
         free(hash_header);
         hash_header = NULL;
         return -1;
     }

    for (i = 0; i < numsSize; i++) {
        v = hash_func(nums[i]);
        hash_mem[i].element = nums[i];
        hash_mem[i].index = i;
        hash_mem[i].next = hash_header[v];
        hash_header[v] = &hash_mem[i];
    }

    return 0;
 }

 struct hash_node *hash_find(int num, int index)
 {
     int v;
    struct hash_node *node_p;

     v = hash_func(num);

    node_p = hash_header[v];
     while (node_p) {
         if (node_p->element == num && node_p->index > index) {
             return node_p;
         }
         node_p = node_p->next;
     }

     return node_p;
 }

 void hash_free(void)
 {
     if (hash_mem) {
         free(hash_mem);
         hash_mem = NULL;
     }

     if (hash_header) {
         free(hash_header);
         hash_header = NULL;
     }
 }

int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    int i, j, need;
    int *res;
    int ret;
    struct hash_node *node_p;

    ret = build_hash(nums, numsSize);
    if (ret != 0) {
        *returnSize = 0;
        hash_free();
        return NULL;
    }
    for (i = 0; i < numsSize; i++) {
        need = target - nums[i];
        node_p = hash_find(need, i);
        if (node_p == NULL) {
            continue;
        }
        res = malloc(2 * sizeof(int));
        if (res == NULL) {
            *returnSize = 0;
            hash_free();
            return NULL;
        }
        res[0] = i;
        res[1] = node_p->index;
        *returnSize = 2;
        hash_free();
        return res;
    }

    hash_free();
    *returnSize = 0;
    return NULL;
}
```

## More
**在广大群友的讨论以及百度之下get到了更多解法**

### 传说中的双向并行二分查找
* 排序方法我就不说了，有各种各样的实现，我个人还是趋向于使用qsort
* 在网上嫖到了[双向并行二分查找的C语言实现](https://blog.csdn.net/weixin_41043240/article/details/79440040)，在此大概贴一个思路，有兴趣的同学自行跳转到上述链接。
* 有一说一这思路不大好说，大家自行根据代码画图来看吧，个人觉得比较烧脑，短时间内写不出来。
```c
int start = 0;
int end = len - 1;

while(start + 1 < end)
{
    mid = start + (end-start) / 2;
    if(numList[start] + numList[end]<target)
    {
        if(numList[mid]+numList[end]<target)
        {
            start=mid;
        }   
        else if(numList[mid]+numList[end]>target)
        {
            start++;
        }   
        else
        {
            index[0]=mid;
            index[1]=end;
            //printf("index:%d,%d\n",index[0],index[1]);
            return index;
        }   

    }
    else if(numList[start] + numList[end]>target)
    {
        if(numList[start]+numList[mid]>target)
        {
            end=mid;
        }   
        else if(numList[start]+numList[mid]<target)
        {
            end--;
        }   
        else
        {
            index[0]=start;
            index[1]=mid;
            //printf("index:%d,%d\n",index[0],index[1]);
            return index;
        }               
    }
    else
    {
        index[0]=start;
        index[1]=end;
        printf("index:%d,%d\n",index[0],index[1]);
        return index;
    }   
}   
```

### 结构体排序后搜索
* leetcode上看到的最优解法
* 主要思路还是qsort排序 + 双指针遍历搜索
* 通过结构体的方式保存了下标，避免了对下标的二次遍历

```c
struct node
{
    int value;
    int index;
};

int comp(const void* a, const void* b)
{
	return ((struct node*)a)->value - ((struct node*)b)->value; 
}


int* twoSum(int* nums, int numsSize, int target, int* returnSize){

    int i;
    struct node* nodes = (struct node*)malloc(numsSize*sizeof(struct node));
    int begin = 0;
    int end = numsSize - 1;
    int *ans=(int *)malloc(sizeof(int)*2);
    *returnSize = 0;

    for (i = 0; i < numsSize; i++)
    {
	    nodes[i].value = nums[i];
	    nodes[i].index = i;
    }

    qsort(nodes, numsSize, sizeof(struct node), comp);

    while(begin < end)
    {
	    if(nodes[begin].value + nodes[end].value == target)
	    {

		    //printf("nodes[begin].value,nodes[end].value %d,%d,%d,%d\n",begin,nodes[begin].value,end,nodes[end].value);
		    ans[0] = nodes[begin].index;
		    ans[1] = nodes[end].index;
            free(nodes);
            *returnSize = 2;
		    return ans;
	    }
	    else if(nodes[begin].value + nodes[end].value > target)
	    {
		    end = end - 1;
	    }
	    else
	    {
	        begin = begin + 1;
	    }
    }
    free(nodes);
    return ans;
}
```