---
title: MFC的线程
toc: true
date: 2014-07-26 22:03:48
tags:
- MFC
categories:
- 学习
- C++
- MFC
---

# MFC的线程

MFC的线程分为用*户界面线程*和*辅助线程*，用户界面线程有消息响应机制，而普通的辅助线程没有消息响应。

在MFC中，一般用全局函数`AfxBeginThread()`来创建并初始化一个线程的运行，该函数有两种重载形式，分别用于创建工作者线程和用户界面线程。两种重载函数原型和参数分别说明如下：

``` c++
CWinThread* AfxBeginThread(
        AFX_THREADPROC pfnThreadProc,
        LPVOID pParam,
        int nPriority = THREAD_PRIORITY_NORMAL,
        UNT nStackSize = 0,
        DWORD dwCreateFlags = 0,
        LPSECURITY_ATTRIBUTES lpSecurityAttrs = NULL
       );//用于创建工作者线程
```
　　PfnThreadProc:指向工作者线程的执行函数的指针，线程函数原型必须声明如下：

``` c++
　　_UINT ExecutingFunction(LPVOID pParam);_
``` 

　　请注意，ExecutingFunction()应返回一个UINT类型的值，用以指明该函数结束的原因。一般情况下，返回0表明执行成功。



- pParam：一个32位参数，执行函数将用某种方式解释该值。它可以是数值，或是指向一个结构的指针，甚至可以被忽略；
- nPriority：线程的优先级。如果为0，则线程与其父线程具有相同的优先级；
- nStackSize:线程为自己分配堆栈的大小，其单位为字节。如
- nStackSize被设为0，则线程的堆栈被设置成与父线程堆栈相同大小；
- dwCreateFlags：如果为0，则线程在创建后立刻开始执行。如果
- CREATE_SUSPEND，则线程在创建后立刻被挂起；
- lpSecurityAttrs：线程的安全属性指针，一般为NULL；

``` c++
    CWinThread* AfxBeginThread(
                CRuntimeClass* pThreadClass,
       　　 　　int nPriority = THREAD_PRIORITY_NORMAL,
       　　　　 UNT nStackSize = 0,
       　　　　 DWORD dwCreateFlags = 0,
       　　　　 LPSECURITY_ATTRIBUTES lpSecurityAttrs = NULL
      　　　　 );
```
pThreadClass 是指向 CWinThread 的一个导出类的运行时类对象的指针，该导出类定义了被创建的用户界面线程的启动、退出等；其它参数的意义同形式1。使用函数的这个原型生成的线程也有消息机制，在以后的例子中我们将发现同主线程的机制几乎一样。

