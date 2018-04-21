---
title: OpenWrt编译
tags:
  - OpenWrt
  - HG255D
categories:
  - 折腾
  - OpenWrt
date: 2014-12-17 10:25:30
---

## 参考文档
- [官方Wiki: OpenWrt Buildroot – Installation](http://wiki.openwrt.org/doc/howto/buildroot.exigence)
- [官方Wiki: OpenWrt Buildroot – Usage](http://wiki.openwrt.org/doc/howto/build)
- [官方Wiki: Image Generator (Image Builder)](http://wiki.openwrt.org/doc/howto/obtain.firmware.generate)
- [官方Wiki: Using the SDK](http://wiki.openwrt.org/doc/howto/obtain.firmware.sdk)
- [开源中国: 编译HG255D的openwrt固件](http://my.oschina.net/osbin/blog/278782)
- [CSDN: HG255D[OpenWrt]从入门到精通](http://blog.csdn.net/eldn__/article/details/9707065)
- [小邹先森博客: 编译个性化的openwrt固件](http://www.zoublog.com/compile-customized-openwrt/)
- [搭建OpenWrt开发环境（包括编译过程）](http://www.cnblogs.com/shudai/p/3256534.html)
- [用ImageBuilder定制自己的openwrt路由器](http://www.it165.net/network/html/201303/1004.html)
- [Openwrt编译 搭建Openwrt SDK](http://see.sl088.com/wiki/Openwrt%E7%BC%96%E8%AF%91_%E6%90%AD%E5%BB%BAOpenwrt_SDK#.E4.BD.BF.E7.94.A8SDK)

## 环境说明
- 操作系统：Fedora 21 - 64Bit
- 路由器： HG255D  CPU：RT3052F 384M FLASH：16M RAM：64M
- OpenWrt: trunk (main development tree) 2014.12.16

## 安装依赖
按照*[官方Wiki: OpenWrt Buildroot – Installation](http://wiki.openwrt.org/doc/howto/buildroot.exigence)*进行安装

> yum install -y subversion binutils bzip2 gcc gcc-c++ gawk gettext flex ncurses-devel zlib-devel make patch unzip perl-ExtUtils-MakeMaker glibc glibc-devel glibc-static quilt ncurses-lib sed sdcc intltool sharutils bison wget openssl-devel

ps: 比官方wiki中多了*openssl-devel*,在fedora 21中*make menuconfig*时提示

> Build dependency: Please install the openssl library (with development headers)

## 从源码编译固件

### 下载更新源码

> svn co svn://svn.openwrt.org/openwrt/trunk/
> svn update

### 下载更新feeds
feeds是OpenWrt所需的软件包，其配置文件为`feeds.conf.default`

> ./scripts/feeds update -a
> ./scripts/feeds install -a

这里的`install`是让这些软件包在`make menuconfig`中可用，并没有真正编译安装，其中更新单个软件包可用`./scripts/feeds install <PACKAGENAME>`实现。

### 针对HG255D进行修改

- 打开对HG255D的支持

编辑target/linux/ramips/image/Makefile,搜索*HG255D*,删除掉所找到的那两行行首的*#*。

- 改电源指示灯不能正常工作的问题

编辑target/linux/ramips/base-files/etc/diag.sh,找到

    hlk-rm04)
            status_led="hlk-rm04:red:power"
            ;;

在下面添加（添加的位置不重要，只要符合语法就行）：

    hg255d)
            status_led="hg255d:power"
            ;;

### 配置软件包并编译

使用如下命令：

> make defconfig
> make prereq
> make menuconfig

`make defconfig`是将配置写入`.config`文件;`make prereq`是相查看编译环境是否准备好;`make menuconfig`会打开配置界面。
使用'y、m、n'按键选择所需的软件包，按下'y'标签变为’<*>‘，表明软件包会被编译进固件；按下'm'标签变为'<M>',表明软件包会被编译，但不会编译进固件（可用opkg进行安装）；按下'n'标签变为‘< >’，表明软件包源码不会被编译。按两个<esc>进行退出。

一切准备好之后就可以进行编译了：
最简单的就是直接输入`make`进行编译。另外也有其他的一些编译参数可用。
- 使用多核处理器：

> make -j 3

`-j`后的数字为处理器核心数+1。

- 输出详细编译信息:

> make V=s

- 在后台进行编译，使用空闲的I/O资源和CPU性能

> ionice -c 3 nice -n 20 make -j 2

- 单独编译一个软件包(cups软件包)

> make package/cups/compile V=99

其他高级方式参考[官方Wiki: OpenWrt Buildroot – Usage](http://wiki.openwrt.org/doc/howto/build)和[搭建OpenWrt开发环境（包括编译过程）](http://www.cnblogs.com/shudai/p/3256534.html)。

### 生成固件的位置

就在当前目录的bin文件夹下。

## 使用Image Builder生成固件

- 下载对应的Image Builder
HG255D的地址在[这里](https://downloads.openwrt.org/snapshots/trunk/ramips/OpenWrt-ImageBuilder-ramips_rt305x-for-linux-x86_64.tar.bz2)

- 解压并进入解压目录，*同源码编译一样针对HG255D修改相关文件*
- 开始编译

编译语法：
编译XXX的固件，包含软件“pkg1 pkg2 pkg3”，不包含“pkg4 pkg5 pkg6”

> make image PROFILE=XXX PACKAGES="pkg1 pkg2 pkg3 -pkg4 -pkg5 -pkg6"

对于所需的软件包，可从前的openwrt中获取：

> echo $(opkg list_installed | awk '{ print $1 }')

还是写成shell脚本方便点：

``` shell
#!/bin/sh
make image PROFILE=HG255D PACKAGES="base-files blkid block-mount busybox cfdisk chat comgt ip ddns-scripts dnsmasq dropbear e2fsprogs ebtables etherwake firewall fstools gre hd-idle hdparm hostapd hostapd-common ip6tables iptables iptables-mod-conntrack-extra iptables-mod-filter iptables-mod-ipopt iptables-mod-ipp2p iw jshn jsonfilter kernel kmod-cfg80211 kmod-crypto-aes kmod-crypto-arc4 kmod-crypto-core kmod-crypto-crc32c kmod-crypto-hash kmod-dnsresolver kmod-ebtables kmod-eeprom-93cx6 kmod-fs-autofs4 kmod-fs-exportfs kmod-fs-ext4 kmod-fs-nfs kmod-fs-nfs-common kmod-fs-reiserfs kmod-fs-vfat kmod-fs-xfs kmod-fuse kmod-gpio-button-hotplug kmod-gpio-dev kmod-gre kmod-hid kmod-hid-generic kmod-ifb kmod-input-core kmod-input-evdev kmod-ip6tables kmod-ipt-compat-xtables kmod-ipt-conntrack kmod-ipt-conntrack-extra kmod-ipt-core kmod-ipt-filter kmod-ipt-ipopt kmod-ipt-ipp2p kmod-ipt-nat kmod-iptunnel kmod-iptunnel4 kmod-ipv6 kmod-leds-gpio kmod-ledtrig-gpio kmod-ledtrig-netdev kmod-ledtrig-timer kmod-ledtrig-usbdev kmod-lib-crc-ccitt kmod-lib-crc-itu-t kmod-lib-crc16 kmod-lib-crc32c kmod-lib-textsearch kmod-mac80211 kmod-macvlan kmod-nls-base kmod-nls-cp437 kmod-nls-iso8859-1 kmod-nls-utf8 kmod-p54-common kmod-p54-usb kmod-ppp kmod-pppoe kmod-pppox kmod-rt2800-lib kmod-rt2800-usb kmod-rt2x00-lib kmod-rt2x00-usb kmod-rt73-usb kmod-rtl8187 kmod-sched-connmark kmod-sched-core kmod-scsi-core kmod-sit kmod-slhc kmod-sound-core kmod-tun kmod-usb-audio kmod-usb-cm109 kmod-usb-core kmod-usb-dwc2 kmod-usb-hid kmod-usb-printer kmod-usb-storage l7-protocols libblkid libblobmsg-json libc libdaemon libext2fs libgcc libgd libip4tc libip6tc libiwinfo libiwinfo-lua libjpeg libjson-c libjson-script liblua libncurses libnfnetlink libnl-tiny libpng libpthread librt libubox libubus libubus-lua libuci libuci-lua libuuid libxtables lua luci luci-app-ddns luci-app-firewall luci-app-hd-idle luci-app-ntpc luci-app-qos luci-app-radvd luci-app-samba luci-app-upnp luci-app-vnstat luci-app-wol luci-base luci-i18n-chinese luci-i18n-english luci-lib-nixio luci-mod-admin-full luci-proto-3g luci-proto-ipv6 luci-proto-ppp luci-theme-bootstrap maccalc miniupnpd mtd netifd ntpclient odhcp6c odhcpd opkg ppp ppp-mod-pppoe procd qos-scripts samba36-server swap-utils swconfig tc terminfo ubox ubus ubusd uci uhttpd uhttpd-mod-ubus vnstat vnstati vsftpd wireless-tools wpad xfs-fsck xfs-growfs xfs-mkfs zlib"
```

## 使用SDK从源码编译软件包

- 下载对应的SDK包
HG255D的下载地址在[这里](https://downloads.openwrt.org/snapshots/trunk/ramips/OpenWrt-SDK-ramips-for-linux-x86_64-gcc-4.8-linaro_uClibc-0.9.33.2.tar.bz2)

- 编译

官方的例子：

``` shell
$ svn export svn://svn.openwrt.org/openwrt/packages/utils/nano package/nano
A    package/nano
A    package/nano/Makefile
Exported revision 20365.
$ grep DEPENDS package/nano/Makefile 
  DEPENDS:=+libncurses
$ svn export svn://svn.openwrt.org/openwrt/trunk/package/libs/ncurses package/ncurses
A    package/ncurses
A    package/ncurses/patches
A    package/ncurses/patches/100-ncurses-5.6-20080112-urxvt.patch
A    package/ncurses/patches/900-terminfo.patch
A    package/ncurses/patches/101-ncurses-5.6-20080628-kbs.patch
A    package/ncurses/patches/500-cross.patch
A    package/ncurses/Makefile
Exported revision 20365.
$ make package/ncurses/compile
Collecting package info: done
 make[1] package/ncurses/compile
 make[2] -C package/ncurses compile
$ make package/nano/compile
 make[1] package/nano/compile
 make[2] -C package/ncurses compile
 make[2] -C package/nano compile
$ make package/index
 make[1] package/index
$ ls bin/packages/mipsel/
libncurses_5.7-2_mipsel.ipk  nano_2.2.3-1_mipsel.ipk  Packages  Packages.gz
```

- 编译自已需要的软件

现在要编译一个ipv6用的6relayd软件，我在[这里](https://dev.openwrt.org.cn/browser#trunk/package/6relayd)找到了。
下载到SDK中：

> svn export svn://svn.openwrt.org.cn/openwrt/trunk/package/6relayd package/6relayd

ps：浏览器的链接是 “https://dev.openwrt.org.cn/browser#trunk/package/6relayd”， 而svn的链接是“svn://svn.openwrt.org.cn/openwrt/trunk/package/6relayd”， 后面是一样的，只需把浏览器链接*#*前换成“svn://svn.openwrt.org.cn/openwrt/”就行了。

编译：

> make package/6relayd/compile
