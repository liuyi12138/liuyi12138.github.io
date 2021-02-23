---
title: PE变形技术
date: 2019-10-13 18:49:36
tags: [逆向,PE]
categories: 网络安全
---

网安组新人任务Week2笔记
<!--more--> 

## PE变形技术分类
### 结构重叠技术
* 在不影响正常性能的前提下，将某些技术进行重叠
* 两个字段中数据发生了重叠但没有发生加载错误
    * 被覆盖的数据可能在另一个结构中无用
    * 有用的数据只对一个数据有用
    * 当被覆盖的数据在两个结构中都有用时必须保证重叠部分数据一致
    * PE加载器不检测所有的字段

### 空间调整技术
* 通过调整IMAGE_DOS_HEADER.e_Ifanew的值实现动态地扩充或缩小DOS STUB块空间，从而达到PE变形的目的
* 复制从PE头开始的所有数据到指定位置
* 可能需要修改的值:
    * IMAGE_DOS_HEADER.e_lfanew —— PE头相对偏移位置
    * IMAGE_OPTIONAL_HEADER.AddressOfEntryPoint —— 程序执行入口
    * IMAGE_OPTIONAL_HEADER.SizeOfImage —— 内存中PE映像的尺寸
    * IMAGE_OPTIONAL_HEADER.SizeOfHeader —— 所有头+节表的大小
    * IMAGE_SECTION_HEADER.VirtualSIze —— 节区的尺寸
    * IMAGE_SECTION_HEADER.PointerToRawData —— 在文件中的偏移

### 数据转移技术
* 将PE中的一部分数据转移到另一个位置
    * 将程序中的变量存储到文件头部结构的某个字段
    * 将代码转移到头部结构的某个连续空间中

#### 变量存储
* 可将.data中的变量存放在IMAGE_DOS_HEADER中，省去一个节表的内容

#### 代码存储
* 可存到IMAGE_DOS_HEADER中或数据目录表中
* 如果空间不够也可将代码分开储存

### 数据压缩技术
* 先压缩代码，在PE头部找一块比较大的连续空间存放压缩用的代码，程序被PE加载器加载后，可通过头部解压程序将代码解压，解压后跳转到解压后的代码

## 变形技术可用的空间
### 文件头部未用的字段
![10.14.1](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.14.1.png)

![10.14.2](https://gitee.com/know_the_emperor/picture/raw/master/pictures/10.14.2.png)

* 不连续的空间可用来存放数据
* 连续但字节数不多的空间可用来存放代码
* 相对较大的连续空间可用来存放较长的指令字节码
    * IMAGE_DOS_HEADER中的54个字节
    * 标准头12个字节
    * 扩展头14个字节
    * 数据目录52个字节
    * 每个节表项20个字节

### 大小不固定的数据块
* DOS STUB
* 数据目录项  NumberOfRvaAndSizes修改数据目录项的项数
* 节表  SizeOfRawData修改节在文件对齐后的尺寸
    * 若修改一个节的尺寸，后续节的起始地址也需要修改

### 因对齐产生的补足空间
* 主要可参考数据压缩技术将0压缩

## PE文件变形原则
* 数据目录表的个数必须大于等于2
* PE头文件中可以没有节的定义，但一定要将IMAGE_FILE_HEADER.NumberOfSections设置为1
* 节的对齐尺寸必须大于等于文件的对齐尺寸

![20191014154208](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191014154208.png)

![20191014154409](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191014154409.png)


