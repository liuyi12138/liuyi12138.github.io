---
title: JavaScript学习笔记１--基本语法
date: 2018-04-07 14:33:28
tags: JavaScript
categories: 编程语言
---

### js与c的比较学习
<!--more--> 

* 所有的声明用var
* 输出用alert
* console.log()页面内输出

#### 数据类型
* 不区分整数与浮点数，字符串用单引号或双引号括起来
* **注意＝＝＝**，NaN！＝ＮaN,isNaN(NaN)=true
* 一般　use strict

#### 字符串
* 多行字符串用“｀”
* 字符串可用＋连接
* s.length
* s.toUpprCase()

##### 字符串函数
* toUpperCase(s)把一个字符串全部变成大写
* toLowerCase(s)把一个字符串全部变成小写
* indexOf('hello')搜索字符串出现的位置，返回数字，未找到返回-1
* substring(x,y)返回指定索引区间的子串

#### 数组
* new Array(1,2,3)或var arr=[1,2,3]创建数组
* arr.length
* arr.indexOf(10)--返回10的索引
* arr.slice(0,3)--返回0-3的值，不包括３(可不要结束索引)(不传递任何参数则截取全部元素)
* arr.push('A','B')--在Array末尾添加元素
* arr.pop()--删除最后一个元素，空数组继续pop不会报错，而是返回undefined
* arr.unshift('A','b')--在Array头部添加元素
* arr.shift()--删除第一个元素，空数组继续shift不会报错，而是返回undefined
* arr.sort()--将数组排序排序
* arr.reverse()--把数组反转
* arr.splice(2,3,'A','B'--删除从２开始的３个元素在此处加上A,B
* arr.concat([1,2,3])--在该数组后加上数组[1,2,3] ,concat()没有修改当前数组而是返回一个新的数组
* arr.join('-')--将数组中的元素用'-'连接，返回一个字符串，如果数组的元素不是字符串，将自动转换为字符串后再连接
* var arr=[[1,2,3],[a,b,c]]多维数组

#### 对象
* JavaScript的对象是一种无序的集合数据类型，它由若干键值对组成。
* js中键必须是字符串，解决方案见Map
* {...}表示对象,　xxx: xxx键值对声明
* '某属性' in 某对象　判断对象中是否存在某属性

#### 条件判断和循环
基本与Ｃ一致

#### Mep和Set
* Map是一组键值对结构
* var m=new Map=['A',1]  ——声明一个Map
* m.set('B',2)  ——添加新的键值对
* m.has('A')  ——是否存在key'A'
* m.get('A')  ——1
* m.delete('A')  ——删除键值对

* Set是一组key的集合，不储存value
* var s1=new Set([1,2,3])  ——创建一个Set
* Set中没有重复的key,重复元素被自动过滤
* 数字与字符串是不同的元素
* s.add(key) ——添加key到Set
* s.delete(key) ——删除key

#### iterable
* Array,Map,Set都属于iterable类型
* for(var x of a)  ——遍历a
* for...of与for..in  ——拒绝for...in 尽量用for...of
* forEach遍历Array

```markdown
a.forEach(function(element,index,array){
//element:指向当前元素的值
//index:指向当前索引
//array:指向Array本身
console.log(element+',index='+index)
})
```

* Set的回调函数前两个参数都是元素本身
* Map的回调函数参数依次为value,key,map本身