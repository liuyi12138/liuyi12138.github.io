---
title: Linux小系统外围文件系统定制
date: 2019-09-17 9:23:36
tags: Linux
categories: Linux
---

**17级种子班Linux初步课程step1**
<!--more--> 

## 基本思路
* 从[Linux启动过程](https://liuyi12138.github.io/2019/09/04/Linux%E5%90%AF%E5%8A%A8%E8%BF%87%E7%A8%8B/)这篇博客的叙述中我们了解到在Kernel内核启动后会解压/boot/initramfs.img到根目录下作为临时文件系统来装载一些模块，之后再重新挂载真正的文件系统
* 我们需要做的则是自己重建一个initramfs.img镜像文件让操作系统启动我们的小系统，从而达到自己定制外围文件系统的目的，也为下一步定制内核打下基础

![基本思路](https://gitee.com/know_the_emperor/picture/raw/master/pictures/2019.9.17.1.png)


## V0.5
### 实验要求
* 利用initrd.img机制，建立一个简单文件系统（v0.5版本），使得内核用该文件系统启动后可以直接获得一个shell

### 实验分析
* 方案1：直接修改系统的initrd.img解压出的目录下的init文件，在switch root之前先启动bash
* 方案2：重新建立一个文件夹加入自己需要的模块
* 个人推荐方案2，后期会有系统大小要求，方案1不便于裁剪

### 实验实现
* 在/boot/grub/grub.conf文件中添加一个开机可选项

![grub.conf](https://gitee.com/know_the_emperor/picture/raw/master/pictures/2019.9.17.2.png)

* 方案1：如下图

![方案1](https://gitee.com/know_the_emperor/picture/raw/master/pictures/6UO0U37VMHX7RTRX349N_VO.png)

* 方案2：
    * 文件结构：
        * bin/  常用命令目录
        * init  启动脚本
        * lib64/  依赖库
    * 我们需要向bin/目录下添加bash、ls、rm、cp等等基础命令，并在lib64中添加相应的依赖库
    * 使用ldd命令可查询命令所需依赖
    * 建议自己写脚本来实现依赖添加
* 使用`find . | cpio -H newc -o | gzip > /boot/initrd.img`命令来将我们自己的文件系统重新打包为镜像

* 自动添加依赖脚本
```sh
cp /bin/$1 bin/
ldd /bin/$1 > /boot/test/lib.txt

for line in `cat /boot/test/lib.txt`
do
if [ ${line:0:6} == "/lib64" ]
then
    echo $line
    cp $line lib64/
fi
done
```

## V0.55
### 实验要求
* 拥有可以挂载原系统能力的(手动挂载)

### 实验分析
* 我们需要挂载文件系统和读取硬盘因此需要加载一些moudles
* mod相关常用命令：
    * lsmod //查看系统中所有已经被加载了的所有的模块以及模块间的依赖关系
    * modinfo //获取模块信息
    * insmod / modprobe  //加载驱动
    * rmmod   //卸载驱动
* 对照解压出的小系统目录加入我们需要的modules(/lib/modules)

### 实验实现
* 加入mod常用的命令道bin并参照V0.5加入依赖(并不是所有的命令都在bin下，可用whereis命令查看命令所在地址)
* 我加入的modules：
    * ext4
    * jbd2
    * mbcache
    * sd_mod
    * crc_t10dif
    * sr_mod
    * cdrom
    * mptspi
    * mptscsih
    * mptbase
    * scsi_transport_spi
* insmod的时候注意依赖关系来决定加载顺序
* init文件内容

![V0.55](https://gitee.com/know_the_emperor/picture/raw/master/pictures/wvl3g0bbka.png)

## V0.6
### 实验要求
* 拥有管理设备能力（udev）
* 自动挂载modules

### 实验分析
* udevd的规则文件在/lib/udev/目录下，配置文件在/etc/udev/目录下，同时还需要/etc/nsswitch.conf配置的名称服务交换，其依赖的库为/lib目录下以libnss开头的文件，将上述文件拷贝到我们的目录下，然后使用/sbin/start_udev命令可以启动udevd服务

### 实验实现
* 注意modules文件夹下的规范，一定要完全按照系统原来的文件夹规范来，不然udev可能找不到
* 还需要将/lib/modules/xxxx.x86_64目录下的各种配置文件拷贝到我们的目录下

* init文件内容
```sh
#!/bin/bash

export PATH=/sbin:/bin:/usr/bin:/usr/sbin:$PATH

# mount some important things
mount -t proc proc /proc/ >/dev/null 2>&1
mount -t sysfs sysfs /sys/ >/dev/null 2>&1

mknod /dev/sda2 b 8 2

/sbin/start_udev

mount -t ext4 /dev/sda2 /root/

/bin/bash
```

## V0.7
### 实验要求
* 拥有login登录能力（多窗口）

### 实验分析
* login程序基于认证体系PAM，配置文件在/etc/pam.d/目录下以及/etc下的security和securetty，相关库文件有/lib64/security/及其依赖的库文件；login还涉及用户组管理/bin/chgrp、/bin/chown、/bin/chmod等，保存用户名的文件/etc/passwd、/etc/group，用户密码文件为/etc/shadow。

### 实验实现
* 自动循环添加/lib64/security/依赖库脚本
```sh
for file in /boot/test/init1/lib64/security/*;do
    echo $file

    ldd $file > /boot/test/lib.txt

    for line in `cat /boot/test/lib.txt`
    do
        if [ ${line:0:6} == "/lib64" ]
        then
           echo $line
            cp $line lib64/
        fi
    done
done
```
* 添加完这些文件后调起login即可
* 跳转窗口使用`/sbin/mingetty /dev/ttyN & login > /dev/ttyN 2> /dev/ttyN < /dev/ttyN &
`

## V0.9
### 实验要求
* 达到由/sbin/init管理的小系统原型

### 实验分析
* V0.9主要是前几个版本的集成，主要难点是将系统交给/sbin/init管理所以很难调试和发现错误
* 而/sbin/init的加载过程详解可以参考[Linux启动过程详解](https://blog.csdn.net/daniellee_ustb/article/details/50939622)

### 实验实现
* 需要拷贝的配置文件有：
    * /etc/inittab  //配置默认运行级别
    * /etc/init/rcS.conf    加载rc.sysinit脚本，//完成系统初始化任务
    * /etc/init/rc.conf //兼容脚本，负责各运行级别的调用处理
    * /etc/init/rcS-sulogin.conf    //为单用户模式启动/sbin/sushell环境
    * /etc/init/control-alt-delete.conf //控制终端下的Ctrl+Alt+Del热键操作
    * /etc/init/start-ttys.conf //配置tty终端的开启数量、设备文件
    * /etc/sysconfig/init   //控制tty终端的开启数量、终端颜色方案
    * /etc/init/tty.conf    //控制tty终端的开启

* 启动/etc/rc.sysinit后将remount_needed函数的调用注释掉，防止挂载主系统根目录
* 其他的问题根据报错信息一点点调试即可
* init文件内容
```sh
#!/bin/bash
export PATH=/sbin:/bin:/usr/bin:/usr/sbin:$PATH

# mount some important things
mount -t proc proc /proc/ >/dev/null 2>&1
mount -t sysfs sysfs /sys/ >/dev/null 2>&1

exec /sbin/init
```

***
**至此linux小系统的外围文件系统已经完成了，在此鸣谢某不知名学长(应该是)留下的[参考资料](http://www.likecs.com/show-49915.html)，接下来定制内核也要好好加油嗷**