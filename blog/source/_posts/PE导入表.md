---
title: PE导入表
date: 2019-10-15 16:15:36
tags: [逆向,PE]
categories: 网络安全
---

网安组新人任务Week2笔记
<!--more--> 

## 导入表简介
* Windows加载器在运行PE时会将导入表中声明的动态链接库一并加载到进程的地址空间，并修正指令代码中调用的函数地址
* 数据目录中有四种类型的数据与导入表数据有关:
    * 导入表
    * 导入函数地址表
    * 绑定导入表
    * 延迟加载导入表

## 导入函数
### 对invoke指令的分解
* 压栈:将要调用的所有参数push到栈中，先push后用的参数
* 段内调用:通过call指令调用一个段内地址
* 无条件转移:call调用出的代码为无条件跳转指令，在该地址获取到导入函数在内存中的VA

### 导入函数地址
* 在PE文件装载进内存前，无条件转移的跳转地址处存放着调用的函数名和在动态链接库里的编号
* 在PE文件装载进内存时，加载器会根据函数名/编号从内存地址空间找到函数的VA，并将找到的函数地址重新覆盖内存中无条件转移的跳转地址处的数据

### 导入函数宿主
* 操作系统会将导入函数所在的动态链接库全部复制到地址空间，这些动态链接库就是导入函数的指令宿主
* 一般情况下动态链接库的装载基地址是固定的，这个地址由IMAGE_OPTIONAL_HEADER.ImageBase决定

![20191015172328](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191015172328.png)

## PE中的导入表
### 导入表定位
* 导入表描述信息位于数据目录第二项
* IAT(Import Address Table 导入函数地址表)描述信息位于数据目录第13项
* 根据描述信息中的RVA计算出其在文件中的偏移地址即可定位导入表和导入函数地址表

### 导入表描述符 IAMGE_IMPORT_DESCRIPTOR
* 导入表数据的起始是一组导入表描述符结构，每组20字节
* 导入表描述符结构中Name1为0代表导入表结束，可由此计算出导入的动态链接库的个数

![20191016151126](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191016151126.png)

* `OriginalFirstThunk`简称桥1，该结构为1个双字,指向一个包含一系列结构的数组，最后以一个内容为全0的结构作为结束。数组中的每一项为一个IMAGE_THUNK_DATA结构
    * 双字的最高位为1，表示导入符号是一个名称
    * 双字的最高位为0，表示导入符号是一个数值，该值是一个RVA
* `NAME1`是一个RVA，指向该结构所对应的DLL文件的名称，该名称以"\0"结尾
* `FirstThunk`简称桥2,与`OriginalFirstThunk`相同，它指向的链表定义了针对Name1这个动态链接库引入的所有导入函数

#### IMAGE_THUNK_DATA
![20191016163342](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191016163342.png)

#### IMAGE_IMPORT_BY_NAME
![20191016205446](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191016205446.png)

**注意Union是为所有的数据仅留存一块公用空间**

### 导入表的双桥结构
* 桥1和桥2都最终都指向引入函数的“编号-名称”(Hint/Name)描述部分

![20191016162504](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191016162504.png)

* 桥1和桥2包含有一个`IMAGE_THUNK_DATA`数组的RVA
* 数组中的每一个`IMAGE_THUNK_DATA`都包含有一个`IMAGE_IMPORT_BY_NAME`结构的RVA
* 桥1桥2指向的IAT和INT存储在不同的位置但存储的内容完全相同

### 导入函数地址表
* 在IAT中相同链接库的函数地址排列在一起，两个链接库的引入函数之前以一个双字的0分隔
* 当PE被加载进虚拟空间以后，IAT的内容会被修改为函数的VA，所以桥2发生断裂，如果没有桥1作为参照就无法重新找到该地址到底调用了哪个函数

## 绑定导入
* 绑定导入的目的是把由Windows家在程序负责的IAT地址导入修正工作提前到加载前进行
* 绑定目录项是数据目录的第12项
* 绑定导入数据的组织方式
![20191020095141](https://gitee.com/know_the_emperor/picture/raw/master/20191020095141.png)
* 绑定导入数据由一系列绑定导入描述符IMAGE_BOUND_IMPORT_DESCRIPTOR结构组成，每一个结构对应一个动态链接库
![20191020094057](https://gitee.com/know_the_emperor/picture/raw/master/20191020094057.png)
    * TimeDateStamp必须与要引用的DLL文件的文件头中的IMAGE_FILE_HEADER.TimeDateStamp保持一致，否则会促使加载器重新计算新的IAT
    * OffsetModuleName包含以第一个IMAGE_BOUND_IMPORT_DESCRIPTOR作为基地址，DLL名称字符串的偏移量
    * NumberOfModuleForwarderRefs描述了紧接在`IMAGE_BOUND_IMPORT_DESCRIPTOR`结构后的另一个结构`IMAGE_BOUND_FORWADRER_REF`数组的元素个数

![20191020094914](https://gitee.com/know_the_emperor/picture/raw/master/20191020094914.png)

* 出于不同的目的(如代码更新、结构调整或实施补丁等),动态链接库中的某些函数的实现代码会被转移到别的动态链接库中。但为了提供向前的兼容，这些动态链接库中还保留了该函数的定义。
* 也就是说，一个导入函数将涉及对多动态链接库函数的调用，数据结构`IMAGE_BOUND_FORWARDER_REF`就是在这样一个背景下产生的，它将引入函数涉及的所有动态链接库都列举出来。该结构的字段定义和`IMAGE_BOUND_IMPORT_DESCRIPTOR`是基本一致的，所以前面的描述“绑定导入数据由一系列的绑定导入描述符`IMAGE_BOUND_IMPORT_DESCRIPTOR`的结构组成”也是成立的

