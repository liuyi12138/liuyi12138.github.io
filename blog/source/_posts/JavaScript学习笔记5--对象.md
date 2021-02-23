---
title: JavaScript学习笔记5--对象
date: 2018-04-07 15:52:08
tags: JavaScript
categories: 编程语言
---

### 慕课网实操学习–对象
<!--more--> 

#### 字符串对象
* stringObject.charAt(index) ——charAt() 方法可返回指定位置的字符。返回的字符　是长度为 1 的字符串
* stringObject.indexOf(substring, startpos)　——indexOf() 方法返回某个指定　的字符串值在字符串中首次出现的位置
* stringObject.split(separator,limit)  ——split() 方法将字符串分割为字符串　数组，并返回此数组
![12.28.1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8xMi4yOC4xLnBuZw?x-oss-process=image/format,png)

* stringObject.substring(startPos,stopPos) ——substring() 方法用于提取字　符串中介于两个指定下标之间的字符
![12.28.２](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8xMi4yOC4yLnBuZw?x-oss-process=image/format,png)

* stringObject.substr(startPos,length) ——substr() 方法从字符串中提取从 startPos位置开始的指定数目的字符串
![12.28.３](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8xMi4yOC4zLnBuZw?x-oss-process=image/format,png)

* Math 对象是一个固有的对象，无需创建它，直接把 Math 作为对象使用就可以调用其所有属性和方法。这是它与Date,String对象的区别
![12.28.4](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8xMi4yOC40LnBuZw?x-oss-process=image/format,png)

#### 数组对象
* 数组对象是一个对象的集合，里边的对象可以是不同类型的。数组的每一个成员对象都有一个“下标”，用来表示它在数组中的位置，是从零开始的
![12.28.5](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8xMi4yOC41LnBuZw?x-oss-process=image/format,png)