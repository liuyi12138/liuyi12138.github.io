---
title: C++刷Leetcode笔记之 127. Word Ladder
date: 2018-08-27 18:11:04
tags: [C++,Leetcode]
categories: 算法
---

**在深信服组期间学习了C++的使用方法，就打算通过刷Leetcode来提高自己的算法能力以及对C++的掌握能力**

至今遇到的最难的题之一，有一种找最短路径的感觉

[Leetcode传送门](https://leetcode.com/problems/word-ladder/description/)

<!--more--> 

# Word Ladder [Difficulty: Medium]
* Given two words (beginWord and endWord), and a dictionary's word list, find the length of shortest transformation sequence from beginWord to endWord, such that:

* Only one letter can be changed at a time.
* Each transformed word must exist in the word list. Note that beginWord is not a transformed word.
* Note:
    * Return 0 if there is no such transformation sequence.
    * All words have the same length.
    * All words contain only lowercase alphabetic characters.
    * You may assume no duplicates in the word list.
    * You may assume beginWord and endWord are non-empty and are not the same.
```
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

```
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```


```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        
    }
};
```
## 我的思路
* 并没能AC(做了一点修改就AC了，开心！！！)
* 首先写了两个轮子，compare函数用来比较两个长度相等的字符串的差异度，findList函数用来寻找一个vector中改变一个字母可以变动的单词
* 然后就用栈来保存之前搜索的路径，类似于BFS
* 难点在于如何判断到了第几层，这时候就使用了另外一个栈同步保存层数
* 局限点在于原数据结构无法很好的保存，由于要防止闭环出现，找到了一个单词就删除掉了，导致无法回退，最终只能做到可以找到一条路径，但无法找到最短路径
```C++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        if (find(wordList.begin(),wordList.end(),endWord) == wordList.end()) return 0;
        vector<string>::iterator findbegin = find(wordList.begin(),wordList.end(),beginWord);
        if (findbegin != wordList.end())
            wordList.erase(findbegin);
        stack<string> words;
        stack<int> nums;
        words.push(beginWord);
        int count = 1;
        int max = 0;
        nums.push(count);
        while (!words.empty()) {
            vector<string> findWords = findList(wordList, words.top());
            printf("%s %d\n",words.top().c_str(),nums.top());
            if (find(findWords.begin(),findWords.end(),endWord) != findWords.end()) return nums.top()+1;
            count = nums.top()+1;
            words.pop();
            nums.pop();
            max = findWords.size();
            for (int i =0; i<max;++i){
                words.push(findWords[i]);
                vector<string>::iterator it = find(wordList.begin(),wordList.end(),findWords[i]);
                wordList.erase(it);
                nums.push(count);
            }
        }
        return 0;
    }
    
    vector<string> findList(vector<string>& wordList, string str) {
        vector<string> findList;
        vector<string>::iterator iter = wordList.begin();
        while (iter != wordList.end()) {
            if (*iter != str && compare(*iter,str) <= 1)
                findList.push_back(*iter);
            iter++;
        }
        return findList;
    }
    
    int compare(string str1, string str2) {
        int size = str1.size();
        int diff = 0;
        for (int i = 0; i<size; ++i) {
            if (str1[i] != str2[i])
                diff++;
        }
        return diff;
    }
};
```

## 参考思路
* 参考答案确实坐实了我的猜想，果然是单源最短路径问题，但由于没有给出图，自己重新构建邻接表又过于麻烦，所以一般是采用栈实现BFS来解题
* 用队列最大的好处就是不用回退，最先出队列的肯定是最短的路径
* 我TM，把我的代码里面的stack换成queue,top换成front就通过了。。。看不透看不透
* 还是贴一下别人的代码吧
* 感觉在搜索方面做的还没我优雅，虽然可能我的代码由于传参耗时会更长。。。
* 值得学习的是queue的巧妙运用，把我的两个queue写成了一个
```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, unordered_set<string>& wordList) {
        wordList.insert(endWord);
        queue<pair<string, int>> que;
        que.push(make_pair(beginWord, 1));
        wordList.erase(wordList.find(beginWord));
        while(!que.empty())
        {
            auto val = que.front();
            que.pop();
            if(val.first == endWord) return val.second;
            for(int i =0; i< val.first.size(); i++)
            {
                string str = val.first;
                for(int j = 0; j < 26; j++)
                {
                    str[i] = 'a'+j;
                    if(wordList.count(str) == 1)
                    {
                        que.push(make_pair(str, val.second+1));
                        wordList.erase(str);
                    }
                }
            }
        }
        return 0;
    }
};

```

### 看了LeetCode上的答案，不得不贴一下超级大佬的优化
* 数据结构上的优化：实际上是使用set来实现队列的功能，find更节省时间
* 算法上的优化：居然还能双向搜索然后随时swap，比较好奇为什么swap还能这么快
```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        int n = beginWord.size(), ret = 1;
        unordered_set<string> wordSet(wordList.begin(), wordList.end()), 
                              beginSet{beginWord}, endSet{endWord}, nextSet;
        if(wordSet.erase(endWord) == 0){
            return 0;
        }
        while(!beginSet.empty() && !endSet.empty()){
            ++ret;
            if(beginSet.size() > endSet.size()){
                swap(beginSet, endSet);
            }
            for(string word: beginSet){
                for(int i = 0; i < n; ++i){
                    for(char j = 'a'; j <= 'z'; ++j){
                        char ch = word[i];
                        word[i] = j;
                        if(endSet.count(word) != 0){
                            return ret;
                        }
                        if(wordSet.count(word) != 0){
                            wordSet.erase(word);
                            nextSet.insert(word);
                        }
                        word[i] = ch;
                    }
                }
            }
            beginSet = nextSet;
            nextSet.clear();   
        }
        return 0;     
    }
};
```
## 感想
* 果然掌握各种数据结构的搭配是第一生产力