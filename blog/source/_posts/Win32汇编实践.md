---
title: Win32汇编实践
date: 2019-10-09 15:54:36
tags: [逆向,汇编]
categories: 网络安全
---

网安组新人任务Week1实践内容
<!--more--> 
## helloWorld
* 任务需求：用win32汇编写出一个弹窗HelloWorld

### 代码编写:
```asm
;.386是伪指令 告诉编译器使用 386指令集
.386

;.model 内存模式[,语言模式][,其它模式]
;flat 是win32程序使用的模式，代码和数据段使用同一个4GB段
;stdcall 是函数调用约定，参数从右往左压栈
.model flat,stdcall

;大小写敏感
option casemap:none

;包含和应用的库文件
include windows.inc
include user32.inc
includelib user32.lib
include kernel32.inc
includelib kernel32.lib

;数据段
.data
szCaption   db  'Dian团队网安组', 0
szText      db  'Hello,World !', 0

;代码段
.code
start:
    invoke MessageBox,NULL,offset szText,offset szCaption,MB_OK
    invoke ExitProcess,NULL
end start
```

### 编译与链接
* 编译：`ml /c /coff helloWorld.asm`
    * /c Assemble without linking 表示只编译不连接
    * /coff generate COFF format object file 生成COFF格式的目标文件
* 链接
    * `link /subsystem:windows helloWorld.obj`窗口类型的程序
    * `link /subsystem:console helloWorld.obj`控制台类型程序

## login
* 任务需求：用win32汇编写出一个控制台程序，请求用户输入用户名和密码，当用户名为root时无论密码为什么都能登录成功，当用户名为其他的时候，密码为另一个固定的密码时才能登录

### 代码编写:
```asm
;.386是伪指令 告诉编译器使用 386指令集
.386

;.model 内存模式[,语言模式][,其它模式]
;flat 是win32程序使用的模式，代码和数据段使用同一个4GB段
;stdcall 是函数调用约定，参数从右往左压栈
.model flat,stdcall

;大小写敏感
option casemap:none

;包含和应用的库文件
include windows.inc
include user32.inc
includelib user32.lib
include kernel32.inc
includelib kernel32.lib
include masm32.inc
includelib masm32.lib

;数据段
.data
colHello        db  'Dian团队网安组登录测试', 0Ah,0
colAccount      db  'please input the account',0Ah,0
colPassword     db  'please input the password',0Ah,0
colOk              db  'login success !',0Ah,0
colWrong           db  'password wrong !',0Ah,0
len   equ   8

root            db  'root',0
truePassword    db  'diangroup',0
account         db  ?
password        db  ?


;代码段
.code
start:
    invoke StdOut, addr colHello
    invoke StdOut, addr colAccount
    invoke StdIn, addr account,len
    invoke StdOut, addr colPassword
    invoke StdIn, addr password,len

    movsx AX, account
    movsx BX, root
    cmp AX,BX
    jz @ok
    
    movsx AX, password
    movsx BX, truePassword
    cmp AX,BX
    jz @ok
    invoke StdOut, addr colWrong
    jmp @end

    @ok:
    invoke StdOut, addr colOk

    @end:
    invoke ExitProcess, 0
end start
```

## decrypt
* 尝试反编译该程序找出解密密码

### C语言代码编写:
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main()
{

    char* code = "Welcome to Dian !";
    int len = strlen(code);
    char password[128];
    char key[128];
    char value[128] = {};
    char decode_value[128] = {};
    printf("please input the password\n");
    scanf("%s",password);
    getchar();
    if(strcmp(password,"Diangroup709") == 0){
        for(int j = 0; j < len; ++j)
            value[j] = code[j] ^ code[len - j - 1];
        printf("%s\n",value);
        FILE *fp =fopen("D:\\dian","w");
        if(fp == NULL)
        {
            return 0;
        }
        fprintf(fp ,"%s",value);
        fclose(fp);

        printf("please input the key to decrypt the file\n");
        gets(key);
        for(int j = 0; j < len; ++j){
            if(strlen(key) > j){
                decode_value[j] = value[j] ^ key[j];
            }

        }

        FILE *fp1 =fopen("D:\\dian","w");
        if(fp1 == NULL)
        {
            return 0;
        }
        fprintf(fp1 ,"%s",decode_value);
        fclose(fp1);

    }
    else{
        printf("wrong\n");
    }
    return 0;
}
```