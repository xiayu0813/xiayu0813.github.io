---
title: FreeRTOS移植-系统基础
tags:
  - FreeRTOS
categories:
  - FreeRTOS移植
  - 系统基础
toc: true
date: 2014-08-31 09:47:43
---

## 编码标准和命名惯例

1. 编码基本符合MISRA标准，除在stdint.h文件中定义的一些类型外，使用C99标准语法。
2. 在变量名前使用u、ul、p等表示变量类型，x表示系统自定义的类型。
3. 函数名前缀prv(private)表示文件中的static函数。
4. 系统API函数前缀有返回数型和所在的文件，例如vTaskDelete在task.c文件中定义，返回void。
5. 宏的小写前缀表示所在的定义头文件，如configUSE_PREEMPTION

## 任务和协程

1. 协程用于小型处理器，目前用的比较少。主要特点是所有的协程使用一个栈，而每一个任务具有独立的栈。
2. 任务有Ready,Running,Blocked,Suspended四种状态，如下图所示：![taskstate](/images/FreeRTOS_TaskState.gif)
3. 任务具用0-configMAX_PRIORTIES-1个优先级，不同的任务可以具有相同的优先级。
4. 数字越小，优先级越低。空闲任务的优先级是0。
5. 任务的形式：
``` c
    void vATaskFunction( void *pvParameters )
    {
        for( ;; )
        {
            -- Task application code here. --
        }

        /* Tasks must not attempt to return from their implementing
        function or otherwise exit.  In newer FreeRTOS port
        attempting to do so will result in an configASSERT() being
        called if it is defined.  If it is necessary for a task to
        exit then have the task call vTaskDelete( NULL ) to ensure
        its exit is clean. */
        vTaskDelete( NULL );
    }
```

## 任务间通信

1. FreeRTOS提供队列(Queues)、二进制信号量(Binay Semaphores)、计数信号量(Counting Semaphores)、互斥量(Mutexes)和递归互斥量(Recursive Mutexes)。
2. 队列传递的是数据的拷贝，同时也可以传递引用。对于指针，队列传递是指针的拷贝而不是所指向内容的拷贝。
3. 二进制信号量和互斥量的不同：互斥量具有优先级继承机制（高优先级可以中断低优先级），这使得二进制信号量更适用于同步，互斥量更适用于互斥；互斥具有令牌（token）,二进制信号量没有。

## 系统文件结构

1. FreeRTOS的系统文件比较少，用到的c文件有tasks.c(任务创建、调度、销毁等相关操作)、queue.c(消息队列的实现)、list.c(链表相关操作)、port.c(硬件相关的系统操作)、heap_x.c(x=1~5，用于内存管理，各不相同)。
2. FreeRTOS的代码组织比较清晰，详见[Source Organization](http://www.freertos.org/a00017.html)
