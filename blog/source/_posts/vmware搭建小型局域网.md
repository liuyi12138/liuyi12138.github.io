---
title: vmware搭建小型局域网
date: 2019-10-29 19:21:36
tags: [计网]
categories: 网络安全
---

网安组计网培训任务
<!--more--> 
## 任务要求
* 通过VMware为虚拟机添加网卡，并给虚拟机配置静态IP、网关和路由(iproute2/net-tools工具配置，或使用配置文件配置)。使得B和C能互ping，且能ping通A
* 使用iptables实现NAT转换，使得B和C能够访问(ping)A的网关和公网
![20191029192157](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191029192157.png)

## 知识整理
### iptables
* 可参考[朱双印个人日志|iptables详解(一): 基本概念](https://www.zsythink.net/archives/1199)
* iptables相当于一个客户端代理，用户通过iptables将安全设定执行安全框架(netfilter)中
* netfilter位于内核空间，用户通过操作位于用户空间的iptables来控制netfilter
* netfilter/iptables组成Linux平台下的包过滤防火墙

## 任务实现
### 静态ip配置
* 修改/etc/sysconfig/network-scripts/中的网卡配置为静态ip，并设置好ip地址、掩码和网关
* 需要注意的问题是需要修改vmware的虚拟网络编辑器中的子网ip，否则系统会先依据虚拟网络编辑器中的ip配置

### 路由配置
* 修改/etc/sysctl.conf中的net.ipv4.ip_forward=1，开启路由转发
* sysctl -p重新加载配置文件
* iptables -F关闭防火墙

### tcpdump抓包
* 由于BC两虚拟机未联网，对虚拟机A的IP2网卡使用tcpdump进行抓包分析

#### tcpdump -i ens34 host 10.1.2.100
* 使用虚拟机C(10.1.2.101) ping 虚拟机B(10.1.2.100)
* 由图可见10.1.2.101和10.1.2.100有来有回，是可以互相ping通的
![20191030094208](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191030094208.png)

#### tcpdump -i ens34 host 8.8.8.8
* 使用虚拟机C(10.1.2.101) ping 8.8.8.8
* 由图可见虚拟机C暂时还无法ping通google的服务，还需要配置iptables
![20191030094643](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191030094643.png)

### iptables配置
* 可参考[朱双印个人日志|iptables详解(十三): NAT](https://www.zsythink.net/archives/1764)

`iptables -t nat -A POSTROUTING -s 10.1.2.0/24 -j SNAT --to-source 192.168.188.128`
* `-t nat`表示操作nat表
* `-A POSTROUTING`表示将SNAT规则添加到POSTROUTING链尾
* `-s 10.1.2.0/24`表示匹配的报文来自10.1.2.0/24
* `-j SNAT`表示使用SNAT动作，对报文进行地址转换
* `--to-source 192.168.188.128`表示将匹配到的报文源修改为192.168.188.128

### tcpdump抓包验证
![20191030154436](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191030154436.png)