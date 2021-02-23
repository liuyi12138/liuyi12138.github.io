---
title: 鸟哥的Linux私房菜学习笔记
date: 2018-04-09 14:18:36
tags: Linux
categories: Linux
---

鸟哥的Linux私房菜—-第零章 计算机概论
<!--more--> 
## 鸟哥的Linux私房菜----第零章  计算机概论


### 0.1电脑

#### 0.11计算机硬件的五大单元

输入单元、输出单元、CPU内部的控制单元、算数逻辑单元、内存
![0.11.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjExLjEucG5n?x-oss-process=image/format,png)

#### 0.12CPU的架构

1.精简指令集（PISC)

PISC的微指令集较为精简，每个指令的执行时间都很短，完成的动作也很单纯，指令的执行性能较佳。



2.复杂指令集（CISC)

CISC在微指令集的每个小指令可以执行一些较低阶的硬件操作，指令数目多而且复杂， 每条指令的长度并不相同。

因为指令执行较为复杂所以每条指令花费的时间较长， 但每条个别指令可以处理的工作较为丰富。



**TIPS:所谓的位指的是CPU一次数据读取的最大量！**



#### 0.13其他单元的设备

1.系统单元：系统单元包括 CPU 与内存及主板相关元件。

  主板上还有很多的连接接口与相关的适配卡（网卡、 磁盘阵列卡、显卡等等）。

2.存储单元：包括内存 （main memory, RAM） 与辅助内存，

  辅助内存就是“储存设备”，包括硬盘、软盘、光盘、磁带等等。

3.输入、输出单元：触摸屏、键盘、鼠标等等，屏幕，打印机，投影机等等。



**CPU=脑袋瓜子；**

**内存=脑袋中放置正在被思考的数据的区块；**

**硬盘=脑袋中放置回忆的记忆区块；**

**主板=神经系统；**

**各项周边设备=人体与外界沟通的手、脚、皮肤、眼睛等；**

**显卡=脑袋中的影像；**

**电源供应器 （Power）=心脏；**





#### 0.15电脑按用途分类

超级计算机、大型计算机、迷你电脑、工作站、微电脑



#### 0.16电脑上面常用的计算单位

 1 Byte = 8 bits





![0.16.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjE2LjEucG5n?x-oss-process=image/format,png)









### 0.2个人电脑架构与相关设备原件

 早期芯片组分南北桥

1）北桥：负责链接速度较快的CPU、内存与显卡接口等元件。

2）南桥：负责连接速度较慢的设备接口， 包括硬盘、USB、网卡等等。



因此目前的主流架构中， 大多将北桥内存控制器整合到 CPU 封装当中了（方便CPU与内存的交流）。





[外链图片转存中...(img-acrItlAD-1570972088936)]





#### 0.21CPU

评判性能指标：频率（只能比较同款CPU的速度）、微指令集、架构、内存容量



**1.CPU的工作频率：外频与倍频**

外频指的是CPU与外部元件进行数据传输时的速度

倍频则是 CPU 内部用来加速工作性能的一个倍数

两者相乘才是CPU的频率速度



超频：将CPU的倍频或者是外频通过主板的设置功能更改成较高频率的一种方式。

     但因为CPU的倍频通常在出厂时已经被锁定而无法修改， 因此较常被超频的为外频。



一般来说，每次频率能够传输的数据量，大多为64 位，这个 64 位就是所谓的“宽度”。

内存使用系统总线带宽来与 CPU 沟通，而显卡则通过PCI-E的序列信道设计来与CPU沟通。

**CPU可以从内存中取得的最快带宽=频率速度*系统总线宽**



**2.超线程 （Hyper-Threading, HT）**

在每一个CPU 内部将重要的寄存器 （register） 分成两群， 而让程序分别使用这两群寄存器。

也就是说，可以有两个程序“同时竞争 CPU 的运算单元”，而非通过操作系统的多任务切换。





#### 0.22内存

个人电脑的内存主要元件为动态随机存取内存（Dynamic Random Access Memory, DRAM），

随机存取内存只有在通电时才能记录与使用，断电后数据就消失了。因此我们也称这种RAM为挥发性内存。



DRAM使用广泛的主要是SDRAM与DDR SDRAM，

DDR是所谓的双倍数据传送速度（Double Data Rate）， 他可以在一次工作周期中进行两次数据的传送。




![0.22.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjIyLjEucG5n?x-oss-process=image/format,png)




**1.多通道设计**

将多个内存汇整在一起，加大总线宽度。

要求：必须要安插两支（或四支）内存，除了容量大小要一致之外，型号也最好相同。



**2.DRAM与SRAM**

SRAM在设计上使用的电晶体数量较多，价格较高，且不易做成大容量，

不过由于其速度快， 因此整合到CPU内成为高速缓存内存以加快数据的存取是个不错的方式。



![0.22.2.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjIyLjIucG5n?x-oss-process=image/format,png)



第二层高速缓存（L2 cache）整合到CPU内部，因此这个L2内存的速度必须要CPU频率相同。

使用DRAM是无法达到这个频率速度的，此时就需要静态随机存取内存（StaticRandom Access Memory, SRAM）。





**3.只读存储器（ROM）**

主板上面如果有内置的网卡或者是显卡时，该功能是否要启动与该功能的各项参数，

是被记录到主板上头的一个称为CMOS的芯片上，这个芯片需要借着额外的电源来发挥记录功能。

而CMOS内的数据通过ROM来读取与更新。



BIOS（Basic Input Output System）是一套程序，这套程序是写死到主板上面的一个内存芯片中，

这个内存芯片在没有通电时也能够将数据记录下来，那就是只读存储器（Read Only Memory, ROM）。



固件（firmware）很多也是使用ROM来进行软件的写入的。



**ROM是一种非挥发性的内存**





#### 0.23显卡  VGA（Video Graphics Array）

显卡主要也是通过CPU的控制芯片来与CPU、内存等沟通.

**显卡需要的内存=分辨率（像素个数）*每个像素占用的容量**



![0.23.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjIzLjEucG5n?x-oss-process=image/format,png)





#### 0.24硬盘与储存设备

电脑系统上面的储存设备包括有：硬盘、软盘、MO、CD、DVD、磁带机、U盘（闪存）、

还有新一代的蓝光光驱等， 乃至于大型机器的区域网络储存设备（SAN, NAS）等等。



在硬盘盒里面其实是由许许多多的圆形盘片、机械手臂、 磁头与主轴马达所组成的。



![0.24.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjI0LjEucG5n?x-oss-process=image/format,png)



实际的数据都是写在具有磁性物质的盘片上头，而读写主要是通过在机械手臂上的磁头（head）来达成。

实际运行时， 主轴马达让盘片转动，然后机械手臂可伸展让磁头在盘片上头进行读写的动作。

另外，由于单一盘片的容量有限，因此有的硬盘内部会有两个以上的盘片。



磁盘的最小物理储存单位是扇区。

同一个同心圆的扇区组合成的圆就是所谓的磁道（track）。

由于磁盘里面可能会有多个盘片，因此在所有盘片上面的同一个磁道可以组合成所谓的柱面 （cylinder）。

**（同心圆外圈的圆比较大，占用的面积多，所以外围的圆会有更多的扇区）**



**传输接口**

传统磁盘接口包括有 SATA, SAS, IDE 与 SCSI 等等。

若考虑外接式磁盘，那就还包括了 USB, eSATA 等等接口



1.SATA接口



![0.24.2.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjI0LjIucG5n?x-oss-process=image/format,png)



SATA线比较窄小，所以对于安装与机箱内的通风都比较好。

由于SATA一条排线仅接一颗硬盘，所以不需要调整跳针。



![0.24.3.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjI0LjMucG5n?x-oss-process=image/format,png)



SATA 传输接口传输时，通过的数据演算法的关系，当传输 10 位编码时，仅有 8 位为数据，

其余 2 位为检验之用。因此带宽的计算上面， 使用的换算 （bit 转 Byte） 为 1:10 而不是 1Byte=8bits。



2.SAS接口

串行式 SCSI （Serial Attached SCSI, SAS）

这种接口的速度比 SATA 来的快，而且连接的 SAS 硬盘的盘片转速与传输的速度也都比 SATA 硬盘好。



![0.24.4.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjI0LjQucG5n?x-oss-process=image/format,png)



3.USB接口

USB接口是外接式的接口，也是目前最常见到的外接式磁盘接口。



![0.24.5.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjI0LjUucG5n?x-oss-process=image/format,png)



**固态硬盘 **

固态硬盘最大的好处是，它没有马达不需要转动，而是通过内存直接读写的特性，

因此除了没数据延迟且快速之外，还很省电。



传统磁盘 （Hard Disk Drive, HDD）

固态硬盘 （Solid State Disk 或 Solid State Driver, SSD）



对磁盘的评判指标：HDD或SSD、容量、缓冲内存、转速





#### 0.25扩展卡与接口

PCIe 的好处：兼容性- x16 的卡安装在 x16 的插槽，但是这个插槽仅有 x4 的电路设计，这张卡也能运行。



一般服务器惯用的扩展卡，大多数都使用 PCIe x8 的接口 。

为了增加扩展卡的数量，因此服务器级的主板才会大多使用到 x8 的插槽

要发挥扩展卡的能力，就得要搭配相对应的插槽才行。



#### 0.26主板

**1.设备I/O位址与IRQ中断信道**

I/O->各设备的门牌号码

IRQ->各个门牌连接到邮件中心（CPU）的专门路径



**2.CMOS与BIOS**

CMOS主要的功能为记录主板上面的重要参数， 包括系统时间、CPU电压与频率、各项设备的I/O位址与IRQ等。

BIOS为写入到主板上某一块 flash 或 EEPROM 的程序，他可以在开机的时候执行，以载入CMOS当中的参数，

并尝试调用储存设备中的开机程序，进一步进入操作系统当中。



**3.连接周边设备的接口**

1.PS/2接口：这原本是常见的键盘与鼠标的接口，不过目前渐渐被USB接口取代，

  甚至较新的主板可能就不再提供 PS/2 接口了。



2.USB接口：通常只剩下 USB 2.0 与 USB 3.0，为了方便区分，USB 3.0 为蓝色的插槽颜色。



3.声音输出、输入与麦克风：这个是一些圆形的插孔，而必须你的主板上面有内置音效芯片时，才会有这三个东西。



4.RJ-45网络头：如果有内置网络芯片的话，那么就会有这种接头出现。 这种接头有点类

似电话接头，不过内部有八蕊线！接上网络线后在这个接头上会有灯号亮起才对。



5.HDMI：如果有内置显示芯片的话，可能就会提供这个与屏幕连接的接口了！

 这种接口可以同时传输声音与影像， 目前也是电视机屏幕的主流连接接口。




![0.26.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjI2LjEucG5n?x-oss-process=image/format,png)







### 0.3数据表示方式
**数字系统**
**文字编码系统**

### 0.4软件程序执行
#### 0.41操作系统
**1.操作系统核心（Kernel）**
操作系统的功能就是让CPU可以开始判断逻辑与运算数值、 让内存可以开始载入/读出数据与程序
码、让硬盘可以开始被存取、让网卡可以开始传输数据、 让所有周边可以开始运行等等。
**总之，硬件的所有动作都必须要通过这个操作系统来达成。**

**2.系统调用（System Call）**
操作系统其实就是核心与其提供的接口工具


![0.41.1.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL2xpdXlpMTIxMzgvRGlhbl90cmFpbmluZy9tYXN0ZXIvcGljdHVyZS8wLjQxLjEucG5n?x-oss-process=image/format,png)

1.操作系统的核心层直接参考硬件规格写成，所以同一个操作系统程序不能够在不一样的硬件架构下运行。
2.操作系统只是在管理整个硬件资源，包括CPU、内存、输入输出设备及文件系统文件。
3.应用程序的开发都是参考操作系统提供的开发接口，
   所以该应用程序只能在该操作系统上面运行而已，不可以在其他操作系统上面运行的。

**3.核心功能**
核心主要是在负责整个电脑系统相关的资源分配与管理， 因此，核心至少也要有这些功能的：
1.系统调用接口（System call interface）
2.程序管理（Process control）
3.内存管理（Memory management）
4.文件系统管理（Filesystem management）
5.设备的驱动（Device drivers）

**4.操作系统与驱动程序**
操作系统必须要能够驱动硬件，如此应用程序才能够使用该硬件功能；
一般来说，操作系统会提供开发接口，让开发商制作他们的驱动程序；
要使用新硬件功能，必须要安装厂商提供的驱动程序才行；
驱动程序是由厂商提供的，与操作系统开发者无关。


#### 0.42应用程序
应用程序是参考操作系统提供的开发接口所开发出来软件，这些软件可以让使用者操作，
以达到某些电脑的功能利用。


### 0.5重点回顾
**1.计算机的定义为：“接受使用者输入指令与数据，经由中央处理器的数学与逻辑单元运算处理后，以产生或储存成有用的信息”**

**2.电脑的五大单元包括：输入单元、输出单元、控制单元、算数逻辑单元、存储单元五大部分。其中CPU占有控制、算逻辑单元，存储单元又包含内存与辅助内存；数据会流进/流出内存是CPU所发布的控制命令，而CPU实际要处理的数据则完全来自于内存；**

**3.CPU依设计理念主要分为：精简指令集（RISC）与复杂指令集（CISC）系统；关于CPU的频率部分：外频指的是CPU与外部元件进行数据传输时的速度，倍频则是CPU内部用来加速工作性能的一个倍数， 两者相乘才是CPU的频率速度；**

**4.新的 CPU 设计中，已经将北桥的内存控制芯片整合到 CPU 内，而 CPU 与内存、显卡沟通的总线通常称为系统总线。 南桥就是所谓的输入输出（I/O）总线，主要在联系硬盘、USB、网卡等周边设备；**

**5.CPU每次能够处理的数据量称为字组大小（word size），字组大小依据CPU的设计而有32位与64位。 我们现在所称的电脑是32或64位主要是依据这个 CPU解析的字组大小而来的！**

**6.个人电脑的内存主要元件为动态随机存取内存（Dynamic Random Access Memory,DRAM）， 至于CPU内部的第二层高速缓存则使用静态随机存取内存（Static RandomAccess Memory, SRAM）；**

**7.BIOS（Basic Input Output System）是一套程序，这套程序是写死到主板上面的一个内存芯片中， 这个内存芯片在没有通电时也能够将数据记录下来，那就是只读存储器（Read Only Memory, ROM）；**

**8.目前主流的外接卡接口大多为 PCIe 接口，且最新为 PCIe 3.0，单信道速度高达1GBytes/s。**

**9.常见的显卡连接到屏幕的接口有 HDMI/DVI/D-Sub/Display port 等等。HDMI 可同时传送影像与声音。**

**10.传统硬盘的组成为：圆形盘片、机械手臂、 磁头与主轴马达所组成的，其中盘片的组成为扇区、磁道与柱面；**

**11.磁盘连接到主板的接口大多为 SATA 或 SAS，目前台式机主流为 SATA 3.0，理论极速可达 600MBytes/s。**

**12.常见的文字编码为 ASCII，繁体中文编码主要有 Big5 及 UTF8 两种，目前主流为 UTF8操作系统（Operating System, OS）其实也是一组程序， 这组程序的重点在于管理电脑的所有活动以及驱动系统中的所有硬件。**

**13.电脑主要以二进制作为单位，常用的磁盘容量单位为Bytes，其单位换算为1 Byte =8bits。**

**14.最阳春的操作系统仅在驱动与管理硬件，而要使用硬件时，就得需要通过应用软件或者是壳程序（shell）的功能， 来调用操作系统操纵硬件工作。目前称为操作系统的，除了上述功能外，通常已经包含了日常工作所需要的应用软件在内了。**