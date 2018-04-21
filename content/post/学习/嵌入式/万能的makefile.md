---
title: 万能的makefile
toc: true
date: 2014-07-26 22:03:48
tags: [makefile]
categories:
- 学习
- 嵌入式
---
万能makefile的要点对于不同的项目，同一个makefile经过很少的修改或完全不用修改就可以使用。

具体来说就是能够自动扫描得到项目所包含的文件(c语言就是.c文件)，对于嵌入式开发还有.S的汇编文件。一般来说工程目录名就是项目名，为了不输入最终的可执行文件名，需要读取出makefile所在的目录名。
<!-- more -->
``` makefile
    EMPTY :=
    SPACE := $(EMPTY) $(EMPTY)
    ProjDir := $(subst $(SPACE),_,$(CURDIR))  # 将目录中的空格用下划线_替换掉

    ProjName := $(notdir $(ProjDir))  # 获取项目名称

    SRCS := $(wildcard *.c) $(wildcard *.S)  # 得到该目录下的.c .S文件列表
    OBJS := $(patsubst %.c,%.o,$(SRCS))  # 将.c替换成.o得到目标
```

嵌入式开发用的相对万能的makefile(开始用，要不断完善)

``` makefile
CC = arm-linux-gcc
LD = arm-linux-ld
OC = arm-linux-objcopy
OD = arm-linux-objdump

CFLAGS := -g -c -o
OCFLAGS := -O binary -S

EMPTY :=
SPACE := $(EMPTY) $(EMPTY)
ProjDir := $(subst $(SPACE),_,$(CURDIR))  # 将目录中的空格替换掉
ProjName := $(notdir $(ProjDir))

SRCS := $(wildcard *.S) $(wildcard *.c)
OBJS := $(patsubst %.c,%.o,$(patsubst %.S,%.o,$(SRCS)))


all: $(OBJS)
	@echo Project files: $(SRCS)
	${LD} -Ttext 0x00000000 -g -o $(ProjName).elf $^
	${OC} ${OCFLAGS} $(ProjName).elf $(ProjName).bin
	${OD} -D $(ProjName).elf > $(ProjName).dis
	
%.o:%.c
	${CC} $(CFLAGS) -c -o $@ $<

%.o:%.S
	${CC} $(CFLAGS) -c -o $@ $<

clean:
	rm -f  *.bin *.elf *.o *.dis
```
