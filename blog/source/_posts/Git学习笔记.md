---
title: Git学习笔记
date: 2018-04-07 14:31:20
tags: Git
categories: 工具使用
---

**Git的常用命令总结**
<!--more--> 
#### 用户登录操作

1.git help ：   显示command的help

2.git config --global user.name"......"  : 输入用户名

3.git config --global user.email"....."  ： 输入用户的email

4.git config --list :  查看git用户信息

#### 创建版本库

1.mkdir  *learnigit*(版本库名称)  ：创建版本库

**TIPS：  版本库可手动创建**

2.cd *d:/rep* ： 跳转到D盘的rep文件

3.pwd ： 查看git目前所在路径

4.git init  ：将目录变为git管理的仓库

5.git add *new_1.txt*  :添加文本new_1.txt到暂存区

**TIPS: 文件名尽量不要含空格和中文**

6.git commit -m”此处应有注释“   ：将暂存区的文件提交到版本库

**TIPS: git commit时若未加 -m”注释“ 输入小写i修改，Esc键返回，大写ZZ退出**


#### 查看及修改

1.git status： 查看状态（略写）

2.git diff 文件名 ： 查看修改内容 （详写）

3.git log ： 查看历史记录 （略写）

**TIPS:当版本较多时，小写q退出**

4.git log --preety=oneline  （详写）

5.git log -p ： 查看每次详细修改内容的diff

6.git log -p -2 ： 查看最近两次详细修改内容的diff

7.git log --stat ： 查看提交统计信息

8.git show ： 显示某次提交的内容  == git log -p

9.cat 文件名 ：查看文件内容


#### 删除、找回，重置文件

1.git reset --hard 版本号（git log 里复制） ：回退到某一版本

2.git reset --hard HEAD^  : 回退一个版本

3.git reset --hrad HEAD^^ : 回退两个版本

4.git reset --hrad HEAD~n ： 回退n个版本

5.git reflog ：查看每一次操作

6.git rm 文件名 ：删除版本库文件

7.git checkout 文件名 : 撤销删除



#### 与远程库有关的操作

1.git remote add origin git @github.com:LiuYI12138/Dian_training.git :绑定远程库与本地仓库

2.git push - u origin master :第一次上传文件

3.git push origin master :一般上传文件

4.git pull ：将远程库的文件同步到本地

5.git remote -v ： 查看远程服务器地址和仓库名称

6.git remote show origin ： 查看远程服务器仓库状态


[**参考链接1**](http://www.mamicode.com/info-detail-1425456.html)

[**参考链接2**](https://www.cnblogs.com/cspku/articles/Git_cmds.html)

[**廖雪峰的Git教程**](https://www.liaoxuefeng.com/wiki/0013739516305929606d