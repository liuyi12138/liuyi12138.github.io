---
title: DLL注入
date: 2019-10-21 10:46:36
tags: [逆向,DLL]
categories: 网络安全
---

网安组新人任务Week3笔记
<!--more--> 

## DLL注入简介
* DLL注入指的是向运行中的其他进程强制插入特定的DLL文件
* 从技术细节上来讲，DLL注入命令其他进程自行调用LoadLibrary() API，加载用户指定的DLL文件
* DLL注入主要使用以下三种方法:
    * 创建远程线程(CreateRemoteThread())
    * 使用注册表(AppInit_DLLs值)
    * 消息钩取(SetWindowsHookEx())

## 创建远程线程
### 主要思路
* 使用进程PID打开进程,获得句柄
* 使用进程句柄申请内存空间
* 把dll路径写入内存
* 创建远程线程,调用LoadLibrary
* 释放收尾工作或者卸载dll

### [参考文档](https://blog.csdn.net/ndl1302732/article/details/82998913)

### 代码实现
```C++
BOOL InjectDll(DWORD dwPID, LPCTSTR szDllPath){
    HANDLE hProcess = NULL, hThread = NULL;
    HMODULE hMod = NULL;
    LPVOID pRemoteBuf = NULL;
    DWORD dwBufSize = (DWORD)(strlen(szDllPath) + 1) * sizeof(char);
    LPTHREAD_START_ROUTINE pThreadProc;

    printf("获取目标进程句柄\n");
    //获取目标进程句柄，获取PROCESS_ALL_ACCESS权限
    if(!(hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwPID)) )
    {
        printf("OpenProcess(%d) failed!!! [%d]\n", dwPID, GetLastError());
        return FALSE;
    }

    //将注入的DLL路径写入目标进程内存
    /*
        //在指定进程的虚拟空间保留或提交内存区域
        LPVOID WINAPI VirtualAllocEx(
        __in      HANDLE hProcess,       //进程句柄
        __in_opt  LPVOID lpAddress,   //地址，为NULL自动找一个合适的地址
        __in      SIZE_T dwSize,           //内存块大小，单位为字节
        __in      DWORD flAllocationType, //分配类型，预定或调拨
        __in      DWORD flProtect             //保护属性
        );
    */
    printf("将注入的DLL路径写入目标进程内存\n");
    //在指定进程的虚拟空间保留或提交内存区域
    pRemoteBuf = VirtualAllocEx(hProcess, NULL, dwBufSize, MEM_COMMIT, PAGE_READWRITE);

    /*
        BOOL WINAPI WriteProcessMemory(
        __in   HANDLE hProcess,         //进程句柄
        __in   LPVOID lpBaseAddress, //写入地址
        __in   LPCVOID lpBuffer,          //源缓冲区
        __in   SIZE_T nSize,                 //缓冲区大小，单位为字节
        __out  SIZE_T *lpNumberOfBytesWritten  //实际写入的字节数
        );
    */
    WriteProcessMemory(hProcess, pRemoteBuf, (LPVOID)szDllPath, dwBufSize, NULL);

    //获取LoadLibraryA()的API地址
    //在Windows系统中kernel32.dll在每个进程中的加载地址是相同的
    printf("获取LoadLibraryW()的API地址\n");
    //创建远程线程 主要目的是调用LoadLibrary
    /*
        HANDLE hThread = CreateRemoteThread( 
            __in HANDLE  hProcess,  //目标进程句柄
            __in_opt LPSECURITY_ATTRIBUTES lpThreadAttributes, //表示线程内核对象的安全属性，一般传入NULL表示使用默认设置
            __in SIZE_T dwStackSize,     //表示线程栈空间大小。传入0表示使用默认大小（1MB）
            __in LPTHREAD_START_ROUTINE lpStartAddress, //表示新线程所执行的线程函数地址，多个线程可以使用同一个函数地址
            __in_opt LPVOID lpParameter,    //线程函数参数
            __in DWORD dwCreationFlags,     //指定额外的标志来控制线程的创建，为0表示线程创建之后立即就可以进行调度，
                                            如果为CREATE_SUSPENDED则表示线程创建后暂停运行，这样它就无法调度，直到调用ResumeThread()
            __out_opt LPDWORD lpThreadId    //将返回线程的ID号，传入NULL表示不需要返回该线程ID号
        )
    */
    //由于LoadLibrary是个宏，而非实际的函数，因此需要使用GetProcAddress并传入LoadLibraryW 或 LoadLibraryA来获取真实地址
    hMod = GetModuleHandle("kernel32.dll");

    pThreadProc = (LPTHREAD_START_ROUTINE)GetProcAddress(hMod, "LoadLibraryA");

    //创建远程线程
    printf("创建远程线程\n");
    hThread = CreateRemoteThread(hProcess, NULL, 0, pThreadProc, pRemoteBuf, 0, NULL);
    WaitForSingleObject(hThread, INFINITE);

    CloseHandle(hThread);
    CloseHandle(hProcess);
    return true;
}
```

## 使用注册表
* 在注册表编辑器中将要注入的DLL路径写入到AppInit_DLLs项目中，将LoadAppIait_DLLs的项目值置为1，重启后，指定DLL会注入所有运行进程
* User32.dll被加载到进程时，会读取AppInit_DLLs注册表，若有值则调用LoadLibrery()加载用户DLL


## 消息钩取
* 用SetWindowsHookEx()安装好消息“钩子”，然后由OS将指定DLL强制追响应进程





















