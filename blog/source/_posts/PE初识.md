---
title: PE初识
date: 2019-10-11 14:51:36
tags: [逆向,PE]
categories: 网络安全
---

网安组新人任务Week2笔记
<!--more--> 

## 一些基本概念

### 地址
* 虚拟内存地址(VA)
    * 虚拟内存大小为4GB
    * 虚拟内存地址 = 进程的基地址 + 相对虚拟内存地址
* 相对虚拟内存地址(RVA)
    * RVA是针对某个模块而存在的
* 文件偏移地址(FOA)
    * FOA与内存无关，是指某个位置距离文件头的偏移
* 特殊地址
    * 特殊地址从某个特定位置开始

### 指针
* 某数据结构中某个字段存储的值为一个地址，则这个字段就是一个指针

### 数据目录
* 记录所以可能的数据类型
* 目前已定义15种(各种表)

### 节
* 节用来存放不同类型的数据
* 不同节具有不同的访问权限
* 节是PE文件中存放代码和数据的基本单元，一个节的所有原始数据必须被加载到连续的内存空间

### 对齐
* 数据在内存中对齐
    * 节在内存中的对齐单位必须至少是一个页的大小
    * 32位系统为4KB，64位系统为8KB
* 数据在文件中对齐
    * 以一个物理扇区的大小作为对齐粒度的值(512字节)
    * 一般PE在内存中的尺寸大于在文件中的尺寸
* 资源文件中资源数据对齐
    * 以双字(4个字节)对齐

## PE文件结构

### 16位系统下的PE结构
* 主要分为DOS头和冗余数据两个部分

![10.11.2](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.2.png)
* DOS MZ头
    * DOS MZ头部分的内容在源程序中是找不到相对于的语句的，其字节码的添加是由链接程序自动实现的
    * DOS MZ头大小为64字节
* DOS Stub大小不固定导致
    * DOS Stub是该程序在DOS系统下运行的指令字节码
    * DOS Stub的大小不固定
* PE头和PE数据区在16位系统下是冗余数据，不会程序执行造成影响

### 32位系统下的PE结构
* 在32位系统中与16位系统刚好相反，DOS头部分都是冗余数据

#### 定位标准PE头
* 由于DOS Stub大小不固定导致DOS头大小不固定
* 字段e_Ifanew为标准PE头的相对偏移量
* `PE_start = DOS MZ基地址 + IMAGE_DOS_HEADER.e_lfanew`

#### PE文件结构
![10.11.3](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.3.png)
* 一般PE头大小为456字节(数据目录表项是16个的情况下)，该结构的大小由`IMAGE_FILE_HEADER.SizeOfOptionalHeader`来确定
* 每个PE中节的数量不固定所以节表大小不固定，每个节的描述信息为40字节,节的数量由`IMAGE_FILE_HEADER.NumberOfSections`来定义
* 节内容如同DOS STub一样大小不固定

#### 具体的PE结构
![10.11.5](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.5.png)
* PE头包含：
* 4字节的标识符号(`Signature`)
* 20字节的基本头信息(`IMAGE_FILE_HEADER`)
* 216字节的扩展头信息(`IMAGE_OPTIONAL_HEADER32`)

## PE文件头部解析
### DOS MZ头`IMAGE_DOS_HEADER`
![10.11.6](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.6.png)

### PE头标识`Signature`
* 内容固定，对应的ASCII码的字符串为`"PE\0\0"`

### 标准PE头`IMAGE_FILE_HEADER`
![10.11.7](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.7.png)

### 扩展PE头`IMAGE_OPTIONAL_HEADER32`
![10.11.13](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.13.png)
![10.11.9](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.9.png)

### 广义PE头`IMAGE_NT_HEADERS`
* 广义上的PE头，是PE部分三个数据结构的组合
![10.11.10](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.10.png)

### 数据目录项 `IMAGE_DATA_DIRECTORY`
* 扩展PE头的最后一个字段`DataDirectoty`定义了PE文件中出现的所有不同类型的数据的目录信息
* 每一种数据类型按照如下数据结构存放
![10.11.11](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.11.png)
* 该数据结构中偏移量是基于`IMAGE_NT_HEADERS`头的

### 节表项 `IMAGE_SECTION_HEADER`
* 节表由很多节表项组成
* 节表项的数量由`IMAGE_FILE_HEADER.NumberOfSections`来定义
![10.11.12](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.11.12.png)
* 该数据结构中的偏移量是基于`IMAGE_SECTION_HEADER`头的

## 数据结构字段解析

### PE头`IMAGE_NT_HEADER`字段
* IMAGE_NT_HEADER.Signature
    * 偏移地址 +0000h
    * PE文件标识，被定义为00004550h,即"PE\0\0"

* IMAGE_NT_HEADER.FileHeader
    * 偏移地址 +0004h
    * 该结构指向IMAGE_FILE_HEADER
    * 被称为标准COFF头

* IMAGE_NT_HEADER.OptionalHeader
    * 偏移地址 +0018h
    * 该结构指向IMAGE_OPTIONAL_HEADER32
    * 是操作系统映像文件独有的头部信息，".obj"目标文件中没有
    * 前10个字段原属于COFF，用来加载和运行一个可执行文件，后21个字段是通过连接器追加的扩展部分




