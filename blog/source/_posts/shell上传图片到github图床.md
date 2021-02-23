---
title: shell上传图片到github图床
date: 2019-04-16 23:54:16
tags: [shell,轮子]
categories: 编程语言
---

**在linux端写markdown需要图床而装picGo失败了，一直无法打开，所以自己写了一个小脚本先用着**

<!--more--> 

```shell
# !/bin/bash

cd /media/liuyi/Linux/github/picture
git pull

cd /home/liuyi/Desktop
mv $1 /media/liuyi/Linux/github/picture/pictures

cd /media/liuyi/Linux/github/picture
git add .
git commit -m "picture"
git push origin master

echo -e "\n"
echo "![](https://gitee.com/know_the_emperor/picture/raw/master/pictures/$1)"
echo -e "\n"

```