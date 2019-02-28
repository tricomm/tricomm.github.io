---
layout: post
title:  "ROS中tf包教程"
date:   2019-01-26 21:02:46
categories: tf
tags: ROS tf
---
# tf包学习
## tf介绍
* tf节点可以使机器人中不同部位的相对坐标系(不同机器人间)以及全局基坐标系之间互相转换.
## tf中的工具
* `tf`包中的`view_frames`节点可以绘制一个坐标链接图(带帧数)
* `rqt_tf_tree`包中的`rqt_tf_tree`节点可以显示由tf广播的不同框架
* `tf`包中的`tf_echo [reference_frame] [target_frame]`可查看一个框架相对于另一框架的位置`