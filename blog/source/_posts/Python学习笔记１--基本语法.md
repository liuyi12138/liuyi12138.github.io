---
title: Python学习笔记１--基本语法
date: 2018-04-07 15:54:01
tags: Python
categories: 编程语言
---

### python与c的比较学习

**python没有分号！！！**

**True的"T"要大写**

## 基本语法
<!--more--> 

### 数据类型和变量
* Python允许用'''...'''的格式表示多行内容
* /除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数
* 还有一种除法是//，称为地板除，两个整数的除法仍然是整数：
* 'Hello, %s' % 'world'(比c语言多一个%)(或者用format)
* 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
* 文件开头写上这两行：
```markdown
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

### 特殊数据结构
#### list (类似于数组但元素的数据类型也可以不同)
* list可以以负数为索引，从尾到头搜索
* classmates.append('Adam')　－往list中追加元素到末尾
* classmates.insert(1, 'Jack')　－把元素插入到指定的位置(从0开始)
* classmates.pop()　－删除list末尾的元素
* classmates.pop(i) －删除指定位置元素(从0开始)

#### turple(元组，一旦初始化就不能修改)
* t = (1)　－定义1这个数　　t = (1,)　－定义一个只含1这个元素的turple
* t = ('a', 'b', ['A', 'B'])　－"可变"的turple
**这里turple里面有一个list，相当于c语言里一个const int*t[n]，但是t里面的指针可以改变指向.**
**c语言中int *const p为常指针，不能改变指针的指向**

#### dict
* dict全称dictionary，在其他语言中也称为map([见JavaScript中的Map,不是map](https://liuyi12138.github.io/2017/12/21/JavaScript%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B01--%E5%9F%BA%E6%9C%AC%E8%AF%AD%E6%B3%95/)
```markdown
a = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
a['Michael']
95
```

* dict内部存放的顺序和key放入的顺序是没有关系的
* d.get('Thomas')　－get方法
* pop(key)　－删除一个key
* dict与list的比较

| 名称 |                 时间与键值对数目的关系    　　　　　　　 |          内存与键值对的关系            |
|:---:|:--------------------------------------------------:|:-----------------------------------:|
|dict |　　　　查找和插入的速度极快，不会随着key的增加而变慢　　　　 |　　　  需要占用大量的内存，内存浪费多　　　|
|list |　　　　　　 查找和插入的时间随着元素的增加而增加 　　　　　　|　　　　　占用空间小，浪费内存很少 　 　　　|

* dict的key必须是不可变对象

#### set
* set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key
* s = set([1, 2, 3]) －创建set
* add(key) －添加元素到set中
* remove(key)　－从set中删除元素
* 两个set可以做数学意义上的交集、并集等操作　　s1 & s2　　 s1 | s2



### 判断与循环
* 选择和判断语句后要加引号＂：＂
* Python的缩进规则，如果if语句判断是True，就把缩进的n行print语句执行了，否则，什么也不做(不需要括号哦)
* input()返回的数据类型是str，str不能直接和整数比较，必须先把str转换成整数
```markdown
s = input('birth: ')
birth = int(s) //类似于强制转换，但注意在python中int()是一个函数
if birth < 2000:
    print('00前')
else:
    print('00后')
```

* range()函数可以生成一个连续的整数数列
* list()函数可以将range生成的数列转换为list
```markdown
list(range(5))
[0, 1, 2, 3, 4]
```

***
### Python初感知
**典型的面向对象编程，感觉和JavaScript十分相似(list像arr,dict像Map,set像Set),感觉十分简洁，规范性弱于C，但是Python和JavaScript的区别暂时还没有看出来,有待日后做专题比较．**