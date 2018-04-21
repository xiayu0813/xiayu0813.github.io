---
title: fedora21配置
tags:
  - fedora21
  - 系统配置
categories:
  - wiki
  - linux
date: 2014-12-27 09:58:14
---

## 安装gnome配置工具

    sudo yum install gnome-tweak-tool

可以开启桌面标、窗口最大最小化按钮和一些shell扩展。

## yum使用axel多线程下载
    sudo yum install axel yum-axelget

测试了下安装`yum-axelget`没有作用，还是要用老方法：
> yum install axel
> wget http://cnfreesoft.googlecode.com/svn/trunk/axelget/axelget.conf 
> wget http://cnfreesoft.googlecode.com/svn/trunk/axelget/axelget.py
> cp axelget.conf /etc/yum/pluginconf.d
> cp axelget.py /usr/lib/yum-plugins

## 添加右键打开命令行终端

> yum install nautilus-open-terminal

## 选择最快的软件源

> yum install yum-fastestmirror

## 修改grub默认启动win7

修改`/etc/grub.d`中的配置文件顺序，将win7的启动项放在第一位。
ps: 我的是win7 x64和fedora21 64位双系统，如果还有其他linux系统的话不知道行不行。

> cd /etc/grub.d/
> mkdir bak
> cp * bak/
> mv 10_linux 20_linux
> mv 30_os-prober 10_os-prober
> grub2-mkconfig -o /boot/grub2/grub.cfg

在升级kernel后再执行`grub2-mkconfig -o /boot/grub2/grub.cfg`命令即可。

另外一种设置默认启动项的方法是（也是官方文档给出的方法）：
ps:在我当前的环境下不起作用，不知道怎么回事，fedora20是可以的。

> cat /boot/grub2/grub.cfg | grep windows

结果是：

>menuentry 'Windows Vista (loader) (on /dev/sda1)' --class windows --class os $menuentry_id_option 'osprober-chain-0E1604A416048EC1' {

接下来使用`grub2-set-default`命令来设置默认启动项，参数是上个命令输出结果单引号里面的内容。最后再使用`grub2-mkconfig`来更新`grub.cfg`文件。

> grub2-set-default 'Windows Vista (loader) (on /dev/sda1)'
> grub2-mkconfig -o /boot/grub2/grub.cfg

## 安装fcitx五笔输入法

- 卸载自带的ibus输入法

> sudo yum remove ibus
> gsettings set org.gnome.settings-daemon.plugins.keyboard active false

- 安装fcitx

> sudo yum install fcitx fcitx-configtool fcitx-table-chinese

- 配置环境变量
在~/.bashrc中添加如下内容,注消或重启。

> export GTK_IM_MODULE=fcitx  
> export QT_IM_MODULE=fcitx  
> export XMODIFIERS="@im=fcitx"

- 配置新世纪五笔码表
将码表文件wbpy.mb放入~/.config/fcitx/table中,重启fcitx。




