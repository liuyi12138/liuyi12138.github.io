---
title: C++刷Leetcode笔记之 98. Validate Binary Search Tree
date: 2018-08-25 18:13:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

经典的二叉搜索树判断的问题，做之前还是百度了一下，效果贼好

[Leetcode传送门](https://leetcode.com/problems/validate-binary-search-tree/description/)

<!--more--> 

# Validate Binary Search Tree [Difficulty: Medium] 
## 题目
* Given a binary tree, determine if it is a valid binary search tree (BST).

* Assume a BST is defined as follows:

    * The left subtree of a node contains only nodes with keys less than the node's key.
    * The right subtree of a node contains only nodes with keys greater than the node's key.
    * Both the left and right subtrees must also be binary search trees.
```
Input:
    2
   / \
  1   3
Output: true


    5
   / \
  1   4
     / \
    3   6
Output: false
Explanation: The input is: [5,1,4,null,null,3,6]. The root node's value
             is 5 but its right child's value is 4.
```


```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isValidBST(TreeNode* root) {

    }
};
```
## 我的思路
* 采用递归的方式，难点在于二叉搜索树要求左子树的所有节点小于根节点，右子树的所有节点大于根节点，所以单纯递归肯定不行，必须携带上层的参数。
* 这里就有一个巧妙的方案，每次通过上层节点的值限定这个节点可取的最大最小值，就不用取递归寻找子树的最大最小值了。
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        if(root == NULL) return true;
        return check(root, INT_MIN, INT_MAX);
    }

    bool check(TreeNode* root, int min, int max) {
        if(root == NULL) return true;
        if(root->val == INT_MIN && root->left != NULL) return false;
        if(root->val == INT_MAX && root->right != NULL) return false;
        if(root->val < min || root->val > max) return false;
        return (check(root->left, min, root->val-1) && check(root->right, root->val+1, max));
    }
};
```

## 参考思路
* 利用栈对二叉树进行前序遍历
* 标答更像是把树看作一个线性表，然后逐个比对判断其是否合乎规则
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        
        if(root == NULL)
            return true;
        
        TreeNode* current = root;
        TreeNode* pre = NULL;
        
        stack<TreeNode*> order;
        
        while(current || !order.empty()) {
            while(current) {
                order.push(current);
                current = current->left;
            }
            current = order.top();
            order.pop();
            if(pre && pre->val >= current->val) return false;
            pre = current;
            current = current->right;
            
        }
        
        return true;
    }
};
```
## 如何判断一棵树为二叉搜索树
[五种方法判断BST](https://blog.csdn.net/qq_30490125/article/details/53135274)