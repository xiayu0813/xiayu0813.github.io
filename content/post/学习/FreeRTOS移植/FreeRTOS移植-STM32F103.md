---
title: FreeRTOS移植-STM32F103
date: 2014-09-14 09:20:22
tags:
- FreeRTOS
categories:
- FreeRTOS移植
- STM32F103
toc: true
---

## 移植环境说明

- keil MDK 4.74
- FreeRTOS v8.12
- 移植目标：STM32F103CBT6
- 使用STM32F10x标准固件库3.5：STM32F10x_StdPeriph_Lib_V3.5.0

## 工程建立

1. 首先建立标准固件库的工程（网上内容比较多，不再缀述）
2. 将FreeRTOS中的Source目录复制到工程目录下，删除Source/portable下不需要的文件夹，保留Keil和MemMang文件。（由于Keil和RVDS相同，FreeRTOS官方文件将程序放在RVDS目录下，因此要先将RVDS下的ARM_CM3文件下复制到Keil文件夹下）
3. 在Keil工程中添加FreeRTOS目录，并添加list.c、queue.c、tasks.c、Source\portable\Keil\ARM_CM3\port.c、Source\portable\MemMang\heap_4.c
4. 复制FreeRTOSV8.1.2\FreeRTOS\Demo\CORTEX_STM32F103_Keil\FreeRTOSConfig.h文件至工程目录下的User（或其他）文件夹。最终的Keil工程目录如下：
![FreeRTOS keil](/images/FreeRTOS_KeilProject.jpg)
5. 在Keil下添加相关的头文件包含目录。

## 修改相关文件

由于官方已经提供port.c，文件的修改也相对简单很多（有时间还是要研究下这个文件）。只需修改中断向量表即可：
所修改的文件为startup_stm32f10x_md.s。在__heap_limit后和PRESERVE8之前添加三行IMPORT语句。

``` asm
__heap_limit

    IMPORT xPortPendSVHandler
    IMPORT xPortSysTickHandler
    IMPORT vPortSVCHandler

    PRESERVE8
    THUMB
```

并修改SVCall Handler、PendSV Handler、SysTick Handler为对应的vPortSVCHandler、xPortPendSVHandler、xPortSysTickHandler。至此就算移植完成。

## 测试
``` c

void vLEDRotorFlashTask( void *pvParameters )
{
    for ( ;; )
    {
        vTaskDelay( 500 );
        QC_LED_Toggle( QC_LEDRotor );
    }

}

void vLEDCommFlashTask( void *pvParameters )
{
    for ( ;; )
    {
        vTaskDelay( 1000 );
        QC_LED_Toggle( QC_LEDComm );
    }
}

void vStartLEDFlashTasks( UBaseType_t uxPriority )
{
    xTaskCreate( vLEDRotorFlashTask, "LEDRotor", ledSTACK_SIZE, NULL, uxPriority, ( TaskHandle_t * ) NULL );
    xTaskCreate( vLEDCommFlashTask, "LEDComm", ledSTACK_SIZE, NULL, uxPriority, ( TaskHandle_t * ) NULL );
}

int main( void )
{

    QC_LED_INIT();

    vStartLEDFlashTasks(mainFLASH_TASK_PRIORITY);
    
    vTaskStartScheduler();
        
    while ( 1 )
    {
    }
}

```

