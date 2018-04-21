---
title: tiny6410点亮LED
toc: true
date: 2014-07-26 22:03:48
tags:
- tiny6410
categories:
- 学习
- 嵌入式
---

裸机程序分为启动代码和C函数

启动代码完成硬件初始化并调用C函数

6410的硬件初始化分为设置CPU（告诉CPU外设的地址范围）和关看门狗（3秒内不关或重置会导致重启）。

    

开发板上的LED顺序为：1-2-4-3

查阅tiny6410核心板可以看出：
LED1——GPK4;
LED2——GPK5;
LED3——GPK6;
LED4——GPK7;
且LED为共阳极接法（阳极接1K上拉至VDD_IO 1.2V?），因此端口输出低电平（0）时LED点亮

查询S3C6410的数据手册可以得到控制GPK端口的寄存器地址如下：
  
| Register | Address    | R/W | Description                     | Reset Value |
| -------- | ---        | --- | ---                             | ---         |
| GPKCON0  | 0x7F008800 | R/W | Port K Configuration Register 0 | 0x22222222  |
| GPKCON1  | 0x7F008804 | R/W | Port K Configuration Register 1 | 0x22222222  |
| GPKDAT   | 0x7F008808 | R/W | Port K Data Register            | Undefined   |
| GPKPUD   | 0x7F00880C | R/W | Port K Pull-up/down Register    | 0x55555555  |

GPKCON0 控制GPK0－7的端口配置，每4位对应一个端口，对于16进制刚好每位对应一个端口，0x1111 0000将GPK4－7设置为输出

GPKPUD  控制GPK端口的上/下拉，每2位对应一个端口，00禁止，01下拉，10上拉。  0x0000 00000将GPK4－7设置为禁止上下拉

GPKDAT［0：15］  配置为输出时，向GPKDAT寄存器写数据控制GPK端口输出的高低电平。
配置为输入时，读GPKDAT寄存器表时端口的状态。  每位对应一个端口。



