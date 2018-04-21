---
title: ROS安装
tags:
  - ROS
categories:
  - ROS
date: 2015-03-21 16:59:18
---

# 参考

- [官方wiki](http://wiki.ros.org/)


# 环境说明

- 操作系统： Ubuntu 14.04.2
- ROS： ROS Indigo

# 安装

直接安照[ros wiki](http://wiki.ros.org/indigo/Installation/Ubuntu)来做。
1. 配置软件源（默认的已经允许，不用再配置）
2. 添加ros软件源并设置keys

        sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'
        wget https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -O - | sudo apt-key add -

3. 更新并安装依赖
        
        sudo apt-get update
        sudo apt-get install xserver-xorg-dev-lts-utopic mesa-common-dev-lts-utopic libxatracker-dev-lts-utopic libopenvg1-mesa-dev-lts-utopic libgles2-mesa-dev-lts-utopic libgles1-mesa-dev-lts-utopic libgl1-mesa-dev-lts-utopic libgbm-dev-lts-utopic libegl1-mesa-dev-lts-utopic

    注：对于Ubuntu 14.04不安装这些依赖

4. 安装完整版ros
        sudo apt-get install ros-indigo-desktop-full
5. 初始化rosdep
        sudo rosdep init
        rosdep update
6. 设置环境变量
        echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
        source ~/.bashrc
7. 安装rosinstall
        sudo apt-get install python-rosinstall

# 建立工作区（ROS Workspace）

1. creat a catkin workspace
        mkdir -p ~/catkin_ws/src
        cd ~/catkin_ws/src
        catkin_init_workspace
2. build the workspace
        cd ~/catkin_ws/
        catkin_make   
3. config the environment
        source devel/setup.bash

