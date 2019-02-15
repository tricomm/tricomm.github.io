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

## 建立 catkin workspace工作空间
* 新建 catkin workspace 
        
        $ mkdir -p ~/catkin_ws/src
        $ cd ~/catkin_ws/
        $ catkin_make
    获得文件结构如
    

* workspace结构:workspaceFile包含编译空间|开发空间|源空间
  
    1 编译空间是编译用空间,在buid文件夹内
    
    2 源空间在src文件夹内内部包含一个或多个catkin oackage包,用来存储源代码.
    <br>src中的CMakeLists.txt
    
    3 开发空间,在devel文件夹内  
    存储可执行文件与库文件
    <br>devel中的setup.sh中的一系列指令用来初始设置,可使用
            
        $ source devel/setup.bash
    进行初始化(如将catkin包加入ROS_PACKAGE_PATH环境变量)
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
* `catkin_create_pkg [包名] [依赖名1]...[依赖名n]` 创建程序包,可设置依赖包
  
  包应当创建在 workspace/src/目录下
  <br>将会自动生成CmakeLists.txt与package.xml
* package.xml结构
```xml   
        <description>The beginner_tutorials package</description>  **描述标签**
        <maintainer email="user@todo.todo">user</maintainer> **维护者标签**
        <license>BSD</license> **许可标签**
        <buildtool_depend>catkin</buildtool_depend>
        <build_depend>roscpp</build_depend>  **依赖标签**
```  

## 程序包依赖关系
* `rospack depends1 [包名]`查看包名的一级依赖关系
  
    依赖关系保存在.../包名/package.xml中
* `rospack depends [包名]`递归检测所有依赖

## 编译ROS程序包
* 前置条件是,系统安装了所需依赖,source环境配置(setup)
        
        $ source /opt/ros/groovy/setup.bash
* `catkin_make` cmake+make 生成makefile.txt并编译
 
    在工作空间目录下,默认编译.../src中代码.
    <br>如该源码不再src中,则应该用`catkin_make --source [my_src]`

## ROS Node节点
* 节点是使用ROS与其他Node通信的可执行文件
* Node节点可以publish or subscribe Topic主题
* Node 可以 provide or use Service服务
* 节点用/划分层次的方式命名,
* `rosnode list` 列出当前活动节点

    使用`rosnode cleanup`来更新活动列表

* `rosnode info [节点名]`获取具体节点信息
* `rosnode ping [节点名]`测试节点运行是否正常
* `rosrun [包名] [节点名]` 运行节点
        
    使用`rosrun [包名] [节点名] __name=[自定义名称]` 自定义节点名,并启动

## ROS master 帮助节点找到互相
*`roscore` 启动ROS master

## ROS Topics主题
* ROS节点通过主题相互通信(主题即是订阅与发布的内容)
* `rostopic` 显示topic主题的信息
    
    `rostopic bw [主题名]`  显示主题使用的带宽
    <br>`rostopic echo [主题名]` 打印消息到屏幕
    <br>`rostopic hz [主题名]` 显示主题的发布率    
    `rostopic [主题名]`     列表打印有关活动主题的信息
    <br>`rostopic pub [主题名]`将数据发布到主题
    <br>`rostopic [主题名]` 类型的打印主题类型 
* `rostopic list` 列出当前所有主题
* `rostopic type [主题名]` 显示主题类型也是主题发送消息的类型

    类型名不以/开头 比如geometry_msgs/Twist 

* `rosmsg show[主题类型名]` 显示该类型的详细信息(数据结构)
* `rostopic pub [主题名] [信息类型] [信息内容1 详细内容2 ....]` 发布信息到主题

```json
例如:
rostopic pub /turtle1/cmd_vel geometry_msgs/Twist -1 -- '{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0,y: 0.0,z: 0.0}}'

对应类型结构为
geometry_msgs/Vector3 linear
  float64 x
  float64 y
  float64 z
geometry_msgs/Vector3 angular
  float64 x
  float64 y
  float64 z

```
## rqt 可视化查看节点交互情况
* rqt 中有许多ROS package 提供了不同的可视化功能
* 使用`rosrun [包名] [节点名]`启动
* `rqt_graph`包的`rqt_graph`节点可以显示系统中节点通讯的结构
* `rqt_plot`包的`rqt_plot`节点可画出发布数据图

## ROS service服务
* 服务允许节点发送 request 并接收 response
* `rosservice list`显示活动中的服务列表
* `rosservice type [服务名]` 获得服务类型(输入输出数据结构)
* `rosservice call [服务名] [args]`调用服务
* `rossrv show [服务类型名]`显示该服务类型详细信息(数据结构)

## ROS parameter server(参数服务)
* 使用YAML标记语言的语法:整数1 浮点数1.1 字符串one 布尔值true 列表[1,2,3] 字典{a: b,c: d}
* `rosparam` 参数相关 
        
        rosparam set [参数名] [值]      设置参数
        rosparam get [参数名]           获取参数
            rosparam get /             显示服务器上所有参数
        rosparam load [加载文件] [命名空间]  从文件读取参数
        rosparam dump [写入文件.yaml]    向文件中写入参数
        rosparam delete                 删除参数
        rosparam list                   列出参数名