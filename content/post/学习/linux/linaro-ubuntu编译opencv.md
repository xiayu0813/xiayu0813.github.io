---
title: linaro ubuntu编译opencv
tags:
  - linaro
  - opencv
categories:
  - 学习
  - linux
date: 2014-12-03 15:24:54
---
		
主要参考：[zedboard板上编译opencv](http://www.eefocus.com/bbs/article_1287_591220.html#top)

为了验证相机能否在开发板上使用，一开始使用V4L2程序无法读出图像，又找到一段opencv的程序，又不想搭建交叉编译环境（装系统+配置太麻烦），想着开发板上也是Ubuntu系统，安装opencv应该没有问题。

## 系统说明
- 开发板：[天嵌科技E9卡片电脑](http://www.embedsky.com/Index.php?s=/Product/show/id/57.html)
    - CPU：Freescale i.MX6Q Cortex A9
    - 内存：2GB　DDR3
    - eMMC：8GB
- 操作系统：Linaro Ubuntu 12.04
- 相机：罗技C270网络高清摄像头

## 准备工作
- 安装libgtk2.0-dev、pkg-config

> sudo apt-get install libgtk2.0-dev pkg-config

- 卸载ffmpeg x264 libx264-dev

> sudo apt-get remove ffmpeg x264 libx264-dev

## 安装依赖
> sudo apt-get install build-essential checkinstall git cmake libjack-jackd2-dev libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev libsdl1.2-dev libtheora-dev libva-dev libvdpau-dev libvorbis-dev libx11-dev libxfixes-dev libxvidcore-dev texi2html yasm zlib1g-dev

ps: libfaac-dev 找不到，就不装了，同时安装ffmpeg时要禁用libfaac。

## 安装gstreamer、gtk、libjpeg
> sudo apt-get install libgstreamer0.10-0 libgstreamer0.10-dev gstreamer0.10-tools gstreamer0.10-plugins-base libgstreamer-plugins-base0.10-dev gstreamer0.10-plugins-good gstreamer0.10-plugins-ugly gstreamer0.10-plugins-bad gstreamer0.10-ffmpeg
> sudo apt-get install libgtk2.0-0 libgtk2.0-dev
> sudo apt-get install libjpeg8 libjpeg8-dev

## 编译安装x264
> wget ftp://ftp.videolan.org/pub/videolan/x264/snapshots/last_stable_x264.tar.bz2
> tar xvf last_stable_x264.tar.bz2
> cd x264-snapshot-20141202-2245-stable
> ./configure --enable-shared --enable-pic
> make
> sudo make install

## 编译安装ffmpeg
> wget http://ffmpeg.org/releases/ffmpeg-0.11.1.tar.bz2
> tar xvf ffmpeg-0.11.1.tar.bz2
> cd ffmpeg-0.11.1
> ./configure --enable-gpl --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libxvid --enable-nonfree --enable-postproc --enable-version3 --enable-x11grab --enable-shared --enable-pic

## 编译安装v4l
> wget http://www.linuxtv.org/downloads/v4l-utils/v4l-utils-0.8.8.tar.bz2
> tar xvf v4l-utils-0.8.8.tar.bz2
> cd v4l-utils-0.8.8
> make
> sudo make install

## 编译安装opencv
下载[opencv-unix2.4.9](http://sourceforge.net/projects/opencvlibrary/files/opencv-unix/),解压至OpenCV-2.4.9
> cd cd OpenCV-2.4.9/
> mkdir build
> cd build
> cmake -D CMAKE_BUILD_TYPE=RELEASE .. (最后有两个点)
> make -j4
> sudo make install

执行cmake命令后在结果里确认以下配置（前面正常安装就没有问题）
> found gstreamer-base-0.10
> GTK+ 2.x: YES
> FFMPEG: YES
> GStreamer: YES
> V4L/V4L2: Using libv4l

## 配置路径
> gedit /etc/ld.so.conf.d/opencv.conf

在文件中加入下边语句，保存
> /usr/local/lib

然后更新ldconfig
> sudo ldconfig /etc/ld.so.conf

> gedit /etc/bash.bashrc:

在 /etc/bash.bashrc:中加入下边语句并保存
> PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig

执行
> export PKG_CONFIG_PATH

## 测试程序
``` c
#include <opencv2/opencv.hpp>
#include <stdio.h>
 int main( int argc, char * argv[] ) {
    // grab one frame from the camera specified via arg[1]
    int camera_id = 0;
 
    if (argc > 1) camera_id = atoi(argv[1]);
 
    cv::VideoCapture cap(camera_id);
 
    if(!cap.isOpened())  // check if we succeeded
    {
        printf("failed to open the camera with id %d.\n", camera_id);
        return -1;
    }
    
    // capture one frame
    cv::Mat frame;
    cap >> frame; // get a new frame from camera
    
    // save to file
    imwrite("camera_captured.png", frame);
 
    return 0;
}
```
保存文件名为man.cpp，用gcc编译，生成可执行文件test，运行`./test 0`当前目录保存获取的图像。
``` c
gcc -o test main.cpp `pkg-config --cflags --libs opencv`
./test 0
```

