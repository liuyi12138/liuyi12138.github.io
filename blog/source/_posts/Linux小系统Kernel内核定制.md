---
title: Linux小系统Kernel内核定制
date: 2019-09-23 8:10:36
tags: Linux
categories: Linux
---

**17级种子班Linux初步课程step2**
<!--more--> 
## Linux内核裁剪
**Linux内核定制的重点在于内核裁剪，我们需要将内核大小减小到4M以下就需要裁剪.config中的很多配置**

### linux内核配置
* 更新GCC版本到4.7以上
* 下载内核源码并解压
* 将原内核/boot/config-xxxx.x86_64文件拷贝到解压出的文件夹下命名为.config
* 使用make localmodconfig命令将已经加载的模块加入.config中，此时编译出的内核是可以使用的，但是大小大概在6.4M左右
* 使用make menuconfig命令来修改内核配置
* makeconfig无法使用可参考[解决方案](https://codeday.me/bug/20190814/1653306.html?tdsourcetag=s_pcqq_aiomsg)

### linux内核裁剪方案
```
* make mrproper
* make clean
* make -j4
* make modules_install
* make install

### General setup
* Kernel compression mode->xz #修改解压方式，大幅减小内核大小

### Processor type and features
* Build a relocatable kernel #官方说明 （建立一个移动的内核，并增加10% 的内核尺寸，运行时会被丢弃），我觉得没有10%
* Enable MPS table #MPS多处理器规范
* Support for extended (non-PC) x86 platforms  #如果选的话，可以选择支持一些32位X86的平台
* kexec system call

### General architecture-dependent options
* Kprobes

### Enable loadable module support
* Source checksum for all modules  #为所有的模块校验源码
* 

### Executable file formats
* Write ELF core dumps with partial segments #此项必不可删

### Networking support
* Networking options
    * TCP
* bluetooth
* Wireless

### Device Drivers
* LED Support #LED支持
* Network device support
    * Wireless LAN
* Multiple devices driver support (RAID and LVM)

### File systems
* Ext2 extended attributes #Ext2支持
* Ext4 Security Labels  #以及去掉所有的安全选项

### Kernel hacking
* Kernel debugging
```

## 内核与外围小系统配置
**当我们将自己定制的内核和自己的外围小系统放在一起时有会出现很多的问题**
* 因为我们的外围小系统是针对2.8版本内核定制的，所以modules部分需要重新定制，建议直接拷贝新的版本的modules文件夹，大概在3M左右

### ssh配置
* 这是我在配置的时候遇到的最坑的问题，由于在定制外围小系统的时候没有加上与ip有关的依赖，所以无法用ifconfig查看ip地址
* 首先是ssh和sshd的配置，依旧是可以用我们的脚本来添加依赖，但是要注意有的依赖在/usr/lib64中而不是全都在/lib64
* 将/etc中的sysconfig、profile、protocols以及/var/empty/sshd拷贝到我们的外围小系统下
* 在进一步解决一些依赖问题后我遇到了eth0只有ipv6地址没有ipv4 的地址的问题，我将/etc/sysconfig/network-scripts/ifcfg-eth0中的dhcp分配ip改为了静态ip
* 可参考[静态ip配置](https://blog.csdn.net/u014439239/article/details/78048950)，但这篇文章中有一点错误在于`
NM_CONTROLLED="yes"
`要改为no，不采用自动分配ip