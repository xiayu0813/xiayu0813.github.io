---
title: 控制icache
toc: true
date: 2014-07-26 22:03:48
tags:
- tiny6410
categories:
- 学习
- 嵌入式
- 裸机程序
---

# 相关概念

基于程序访问的局限性，在主存和CPU 通用寄存器之前设置了一类高速的、容量较小的存储器，把正在执行的指令地址附件的一部分指令或数据从主存调入这类存储器，供CPU 在一段时间内使用，这对提高程序的运行速度有很大的作用。这类介于主存和CPU 之间的高速小容量存储器称作高速cache。

比较常见的cache 包括icache 和dcache。icache 的使用比较简单，系统刚上电时，icache中的内容是无效的，并且icache 的功能是关闭的，往 *CP15 协处理器中的寄存器1 的bit[12]* 写1 可以启动icache，写0 可以停止icache。icache 关闭时，CPU 每次取指都要读主存，性能非常低。因为icache 可随时启动，越早开icache 越好。

与icache 相似，系统刚上电时, dcache 中的内容是无效的，并且dcache 的功能是关闭的，往 *CP15 协处理器中的寄存器1 的bit[2]* 写1 可以启动dcache，写0 可以停止dcache。因为dcache必须在启动mmu 后才能被启动，而对于裸机而言，没必要开mmu，所以本教程的程序将不会启动dcache。

# 控制程序

``` 
    #ifdef CONFIG_SYS_ICACHE_OFF
    bic r0, r0, #0x00001000 // 关闭icache
    #else
    orr r0, r0, #0x00001000 // 打开icache
    #endif
    mcr p15, 0, r0, c1, c0, 0
```
