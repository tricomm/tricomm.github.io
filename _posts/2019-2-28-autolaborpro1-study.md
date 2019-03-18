---
layout: post
title:  "无人小车autolabor的文档学习"
date:   2019-02-28 09:02:46
categories: autolabor
tags: ROS 串口 autolabor
---
# autolabor无人小车学习笔记

## 串口结构
1. 切换为上位机模式后小车会在`/dev/ttyUSB0`处(需要赋权限才可操作)
## 上位机控制

1. autolabor ros驱动包(底层驱动),ros键盘控制包下载并在ros工作空间中编译,注意权限设置为777
2. 键盘控制行走
   1. 包`autolabor_keyboard_control`下的add_keyboard_udev脚本用来注册电脑键盘 直接./add_keyboard_udev运行
   
   2. 包`autolabor_pro1_driver/launch`中的`driver.aunch`用来配置设备(串口名等)
        <br>包`keyborad_move.launch`中多一个键盘节点

   3. `roslaunch keyborad_move.launch`后开始键盘控制 
        <br>`9`开启键盘控制 
        <br>`0`停止键盘控制
        <br>`1`加线速度
        <br>`2`减线速度
        <br>`3`角速度增加
        <br>`4`角速度减少
## 网络连接
1. 开启热点 
2. ssh链接小车
<br>10.42.0.1
3. 配置文件
     ```
     export ROS_MASTER_URI=http://10.42.0.1:11311
     export ROS_IP=10.42.0.138
     ```