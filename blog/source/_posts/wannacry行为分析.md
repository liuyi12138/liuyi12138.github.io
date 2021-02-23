---
title: wannacry行为分析
date: 2019-11-8 8:10:28
tags: [逆向,病毒]
categories: 网络安全
---

网安组wannacry分析
<!--more--> 
## 使用PEID进行查壳和语言分析
* 该病毒使用的是VC++6.0(这玩意真的不大好用哦)
* 没有加壳，子系统是Win32 GUI

## 使用LordPE分析
* 主要分许导入表，分析病毒的行为
* 从导入表中整理出来的API接口如下:
```markdown
文件操作:
GetFileSizeEx
ReadFile
WriteFile
CopyFileA
CreateFileA
GetFileAttributesW
SetFileAttributesW
SetFilePointer
SetFileTime
CreateDirectory
SetCurrentDirectory	//切换当前进程的当前工作目录
GetWindowsDirectory	//获取Windows目录的完整路径名

服务操作:
CreateServiceA
OpenServiceA
StartServiceA
CloseServiceHandle

注册表操作:
RegCreateKeyW
RegSetValueExA
RegQueryValueExA
RegCloseKey

进程操作:
CreateProcessA
TerminateProcess	//终止指定进程及其所有的线程。
GetExitCodeProcess	//获取一个已中断进程的退出代码。

其他操作:
LoadLibraryA
GetProcAddress	//检索指定的动态链接库(DLL)中的输出库函数地址

/*
CriticalSection是每个线程中访问临界资源的那段代码
不论是硬件临界资源，还是软件临界资源，多个线程必须互斥地对它进行访问
*/
InitializeCriticalSection
DeleteCriticalSection
LeaveCriticalSection
EnterCriticalSection

资源操作
SizeofResource
LockResource
LoadResource
FindResourceA
```
* 从API接口中我们可以看出，很多接口都涉及到了多线程的问题，需要我们重点了解一下

## 火绒剑模拟运行
* 使用火绒剑对wannacry进行行为监控发现只有文件操作和进程操作，像注册表和网络监控就没有获取到信息

### 文件监控
* wannacry的文件操作主要是打开文件和设置文件安全信息，并不能获取太多信息

### 进程监控
* 跟之前分析的一样，确实监测到的进程动作最多，主要有以下动作
    * PROC_exec   创建进程
    * PROC_exec   跨进程恢复线程
    * PROC_open   打开进程
    * PROC_readvm 跨进程读内存(尤其多)

## 感染结果观察
* 感染后除了桌面上出现一些与wannacry有关的文件外没有太多变化
* 运行了一个exe文件后弹出如下窗口，并更改了桌面背景图,该窗口关闭后每隔一段时间弹出一次

![20191108150711](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191108150711.png)

* 一些软件可以正常运行，它主要感染的还是图片和文档等文件，当然一般是这些文件更值钱

![20191108151027](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191108151027.png)

* 这些文件被感染成了`@Please_Read_Me@.txt`和`@WanaDecryptor@.exe`，`@Please_Read_Me@.txt`内容如下
```txt
Q:  What's wrong with my files?

A:  Ooops, your important files are encrypted. It means you will not be able to access them anymore until they are decrypted.
    If you follow our instructions, we guarantee that you can decrypt all your files quickly and safely!
    Let's start decrypting!

Q:  What do I do?

A:  First, you need to pay service fees for the decryption.
    Please send $300 worth of bitcoin to this bitcoin address: 115p7UMMngoj1pMvkpHijcRdfJNXj6LrLn

    Next, please find an application file named "@WanaDecryptor@.exe". It is the decrypt software.
    Run and follow the instructions! (You may need to disable your antivirus for a while.)
    
Q:  How can I trust?

A:  Don't worry about decryption.
    We will decrypt your files surely because nobody will trust us if we cheat users.
    

*   If you need our assistance, send a message by clicking <Contact Us> on the decryptor window.
 
```

* 按照提示恢复了部分文件，文件恢复了但是那两个文件还在

![20191108151619](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191108151619.png)
