---
title: ROS Package
tags:
  - ROS
categories:
  - ROS
date: 2015-03-21 18:44:08
---

# 参考

- [官方wiki](http://wiki.ros.org/)
- [永顿007的博客](http://blog.sina.com.cn/s/articlelist_1712413141_7_1.html)
- [古月居](http://blog.csdn.net/hcx25909/article/category/1191901)
- 《开源机器人操件系统——ROS》 张建伟 张立伟 胡颖 张俊 编著
- 《ROS By Example》 R.Patrick Goebel

# 环境说明

- 操作系统： Ubuntu 14.04.2
- ROS： ROS Indigo

# ROS文件系统工具

ROS系统工具基本上以ros开头，后接命令作用，同时支持TAB补全。

- rospack:  获取package信息
    比如：获取package路径
        rospack find [package_name]
- roscd: 相当于linux的cd命令，用于切换目录
    比如： 切换到存储log的目录
        roscd log
- rosls: 相录于linux的ls命令

# catkin Package介绍

catkin Package有三点要求：
1. 必须包含package.xml文件，该文件提供了package元数据
2. 必须包含CMakeLists.txt文件
3. 一个文件夹只能包含一个package

一个包含packages的catkin workspace的目录如下：
    
    workspace_folder/        -- WORKSPACE
      src/                   -- SOURCE SPACE
        CMakeLists.txt       -- 'Toplevel' CMake file, provided by catkin
        package_1/
          CMakeLists.txt     -- CMakeLists.txt file for package_1
          package.xml        -- Package manifest for package_1
        ...
        package_n/
          CMakeLists.txt     -- CMakeLists.txt file for package_n
          package.xml        -- Package manifest for package_n

# 创建catkin Package

创建命令为：

    catkin_create_pkg <package_name> [depend1] [depend2] [depend3]

在catkin工作区中进行,创建beginner_tutorials，其依赖为std_msgs, roscpp, rospy

    cd ~/catkin_ws/src
    catkin_create_pkg beginner_tutorials std_msgs rospy roscpp

# 编译Packages

`catkin_make`命令会自动识别该工件区内的packages,并进行编绎。

    catkin_make
    catkin_make install

