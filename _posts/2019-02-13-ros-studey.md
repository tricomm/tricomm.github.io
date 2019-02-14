---
layout: post
title:  "ROS学习随笔"
date:   2019-01-26 21:02:46
categories: ROS
tags: ROS
---
# ROS 学习随笔
## [ROS框架安装](http://wiki.ros.org/kinetic/Installation)
* ROS有许多不同版本,不同版本支持不同系统
* 我的ROS版本是 ROS Kinetic,它支持unbuntu15.10/16.04
## ROS文件系统
* Packages:软件包,ROS应用代码的组织单元
* package.xml 软件包信息描述,定义软件包间的依赖关系
* `rospack` 获取包相关信息

    `rospack find [包名]` 获取包名的路径
* `roscd [包名]` 将工作目录切到包名中os 
  
    只能切换到包含在`ROS_PACKAGE_PATH`环境变量中的包
