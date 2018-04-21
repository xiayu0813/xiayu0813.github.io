---
title: "ROS Nodes,Topics,Services and Parameters"
tags:
  - ROS
categories:
  - ROS
date: 2015-03-22 10:18:26
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

# ROS Nodes

Nodes是ROS中可通信的节点。

> ROS nodes use a ROS client library to communicate with other nodes. Nodes can publish or subscribe to a Topic.Nodes can provide or use a Service.

1. 运行ROS时首先要运行的一个命令是`roscore`,这提供了ROS的运行环境。
2. 使用`rosnode`

    `rosnode`显示正在运行的nodes的信息。
    在新的命令窗口运行`rosnode list`,可以列出正在运行的nodes。
    `rosnode inof [node_name]`命令可以显示出该nodes的信息。

3. 使用`rosrun`

    命令格式为`rosrun [package_name] [node_name]`。
    比如在 *新命令窗口* 运行 `rosrun turtlesim turtlesim_node`会出现一个蓝色背景中央是一个乌龟的窗口（详见官方wiki）。

# ROS Topics

1. 在三个命令窗口分别运行`roscore`，`rosrun turtlesim turtlesim_node`，`rosrun turtlesim turtle_teleop_key`。此时将焦点定位到最后一个命令窗口后就可以用箭头控制乌龟运动了。这是一个node间通信的实例，节点turtlesim_node和节点turtle_teleop_key是在ROS Topic之下进行通信的。

    > turtle_teleop_key is *publishing* the key strokes on turtlesim_node