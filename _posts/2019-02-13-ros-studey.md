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
  
    只能切换到包含在`ROS_PACKAGE_PATH`环境变量中的包<br>log包保存了ROS的日志文件
* `rosls [包名]`对这个包名执行ls命令显示其中包含内容
* 新建 catkin workspace 
        
        $ mkdir -p ~/catkin_ws/src
        $ cd ~/catkin_ws/
        $ catkin_make
    获得文件结构如
        workspaceFile包含build|devel|src
        <br>src中的CMakeLists.txt
        <br>devel中的setup.sh中的一系列指令用来初始设置,可使用
            
        $ source devel/setup.bash
    进行初始化(如将工作区加入ROS_PACKAGE_PATH环境变量)

## 建立ROS package程序包
* 程序包组成结构
  
        my_package/
        CMakeLists.txt  
        package.xml 程序包元信息
* 程序包可放在catkin workspace中,结构如
  
        workspace_folder/        -- WORKSPACE
            src/                   -- SOURCE SPACE
                CMakeLists.txt       -- 'Toplevel' CMake  file, provided by catkin
            package_1/
                CMakeLists.txt     
                package.xml   包1清单     
         ...
             package_n/
                 CMakeLists.txt     
                 package.xml  包n清单
* `catkin_create_pkg [包名] [依赖名1]...[依赖名n]`
  
  包应当创建在 workspace/src/目录下

## 程序包依赖关系
* `rospack depends1 [包名]`查看包名的一级依赖关系
  
    依赖关系保存在.../包名/package.xml中
* `rospack depends [包名]`递归检测所有依赖