---
title: wannacry源码分析
date: 2019-11-9 10:53:23
tags: [逆向,病毒]
categories: 网络安全
---

网安组wannacry分析
<!--more--> 

## 整体结构分析
* 由于时间原因，此博文只分析了wcry.exe主程序的初始化部分
* 下图是wannacry的结构图

![20191109164305](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191109164305.png)

* 主函数的C语言代码如下，对初始化部分做了注释

![20191109164555](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191109164555.png)

## 初始化部分分析
### 设置注册表项
* 主要作用是在`\HKEY_LOCAL_MACHINE\SOFTWARE`下添加一个注册表项，名称为wd,其值为wannacry的程序路径
![20191109110418](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191109110418.png)

* 运行后查询注册表结果如下
![20191109105629](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191109105629.png)

### 释放资源文件
* 此函数不太好分析，但内部调用了很多与资源有关的函数(如下图)

![20191109154147](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191109154147.png)

* 通过动调发现该函数释放出了很多资源文件
![20191109154000](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191109154000.png)
