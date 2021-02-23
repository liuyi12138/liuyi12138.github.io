---
title: Linux启动过程
date: 2019-09-04 14:47:36
tags: Linux
categories: Linux
---

**17级种子班Linux初步课程step0**
<!--more--> 

## 大体启动流程

![Start](https://gitee.com/know_the_emperor/picture/raw/master/pictures/1440487-20180825104814628-14960076.png)

## 具体流程
### MBR引导加载(Boot Loader)
* boot loader将控制权交给对应操作系统的loader，让它负责去启动操作系统(OS Loader)
![Boot Loader](https://gitee.com/know_the_emperor/picture/raw/master/pictures/1521773404296449.png)


### grub(Linux的Loader使用的就是grub)
* Linux所在分区的boot sector就是存放着stage1文件的内容，同时默认Linux启动的话，也需要把stage1中的引导代码安装到MBR中的boot loader中
* stage1完成了主程序的引导后，主引导程序开始加载配置文件，但是加载这些配置文件之前需要有文件系统的支持，GRUB的内置文件系统其实是依靠stage1_5那些文件定义的，而且有不同文件系统的stage1_5(/boot/grub)
* 而后开始读取stage2开始真正地读取配置文件grub.conf,解析/boot/grub/grub.conf文件

### 加载系统内核kernel
* MBR将内核文件（代码）载入物理内存中执行，内核就是/boot/vmlinuz.x86_64，观察该文件，发现这是一个压缩镜像文件,控制权转交给内核后，内核重新检测各种硬件信息
* 这时候内核还没有文件系统的概念，没有文件系统就没办法挂载根目录，想要挂载根目录就需要相应的模块支持，内核需要文件系统来加载提供这些程序功能的模块(鸡与蛋的问题)
* 所以先采用载入临时根文件系统(/boot/initramfs.img)把其解压成根目录，然后内核就可以在这个虚拟的根文件系统上加载驱动程序，之后释放根文件系统，最后开始正常的启动过程。
* initramfs.img解压
```
# file initramfs-2.6.32-431.el6.x86_64.img //检查initramfs文件类型
//initramfs-2.6.32-431.el6.x86_64.img: gzip compressed data, from Unix, last modified: Wed Sep  4 01:05:16 2019, max compression
//所以需要先用gzip解压
# mv initramfs-2.6.32-431.el6.x86_64.img initramfs-2.6.32-431.el6.x86_64.img.gz //gzip解压文件必须以.gz后缀
# gzip -d initramfs-2.6.32-431.el6.x86_64.img.gz
# file initramfs-2.6.32-431.el6.x86_64.img //再次检查文件类型
//initramfs-2.6.32-431.el6.x86_64.img: ASCII cpio archive (SVR4 with no CRC)
//查看需要借助cpio命令
# mkdir init
# cd init
# cpio -id < initramfs-2.6.32-431.el6.x86_64.img//解压到init目录下
```
* 解压之后的内容类似于真正/目录下内容，这是因为这是一个最小化的Linux根文件系统。内核就是先把这个文件展开，形成一个虚拟文件系统(在/目录下)，内核借虚拟文件系统装载必要的模块，完成后释放该虚拟文件系统并挂载真正的根目录
![initramfs](https://gitee.com/know_the_emperor/picture/raw/master/pictures/%7DGK6%7D49Z%7BE%5D4FVERZ4V7LNG.png)

### 启动init
* 内核完成硬件检测和加载模块后，内核会呼叫第一个进程，就是/sbin/init，至此内核把控制权交给init进程
* 初始化配置文件/etc/inittab决定操作系统的runlevel
![inittab](https://gitee.com/know_the_emperor/picture/raw/master/pictures/JREK%5DTOUEGMJ8%7DARWP%7E6PTI.png)
* 系统初始化脚本/etc/rc.d/rc.sysinit设置主机名，挂载/etc/fstab中的文件系统，修改/etc/sysctl.conf 的内核参数等各项系统环境
* 开机启动信息存放在/var/log/dmesg中
* /etc/rc.d/rc脚本
