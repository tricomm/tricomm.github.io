---
layout: post
title:  "ros的cpp库使用建立ROS接收器与发布器"
date:   2019-01-26 21:02:46
categories: ROSCpp
tags: ROS C++ ROS库

---
# ROS/ROS.h的使用,创建接收器与发布器
## 常用依赖库

发布器
```cpp
#include "ros/ros.h"
#include "std_msgs/String.h"

#include <sstream>


int main(int argc, char **argv)
{
 
  ros::NodeHandle n;//节点句柄

 
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);//准备发布话题 话题类型模板给出,话题名,话题长度参数给出

  ros::Rate loop_rate(10);//ros::Rate对象指定自循环频率

 
  int count = 0;
  /*
1 SIGINT 被触发 (Ctrl-C)
2 被另一同名节点踢出 ROS 网络
3 ros::shutdown() 被程序的另一部分调用
4 节点中的所有 ros::NodeHandles 都已经被销毁 
时ros::ok()为false
*/
  while (ros::ok())
  {
  
    std_msgs::String msg;

    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();

    ROS_INFO("%s", msg.data.c_str());//代替printf/cout

    chatter_pub.publish(msg);//publish之前需要一段时间来通知关于这个主题的发布器与订阅器

    ros::spinOnce(); //执行订阅者的回调函数

    loop_rate.sleep();
    ++count;
  }


  return 0;
}
```
接收器
```cpp
#include "ros/ros.h"
#include "std_msgs/String.h"

//回调函数,参数为话题中的消息
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
 //任何ROSnode都要先初始化
  ros::init(argc, argv, "listener");

  //定义Node句柄,会完全初始化此节点
  ros::NodeHandle n;


 //告诉mastet node 想接收消息的方式,如下为接收chatter主题,收到是调用chatterCallback回调函数,缓存队列1000
  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

  
  //进入循环 ctrl-C时退出或被master node关闭
  ros::spin();//循环执行回调函数

  return 0;
}
```
CMakeLists.txt 在包目录下创建包时生成 最底部添加内容
```
cmake_minimum_required(VERSION 2.8.3)
project(beginner_tutorials)

## Find catkin and any catkin packages
find_package(catkin REQUIRED COMPONENTS roscpp rospy std_msgs genmsg)

## Declare ROS messages and services
add_message_files(FILES Num.msg)
add_service_files(FILES AddTwoInts.srv)

## Generate added messages and services
generate_messages(DEPENDENCIES std_msgs)

## Declare a catkin package
catkin_package()

## Build talker and listener

##包含头文件的目录 
##$ {Boost_INCLUDE_DIRS} $ {catkin_INCLUDE_DIRS}变量中有Boost与catkin的库目录 
include_directories(include ${catkin_INCLUDE_DIRS})

####构建名为talker的可执行文件,它由src/talker.cpp源文件构成
add_executable(talker src/talker.cpp)
##指定链接库位置将talker与变量${catkin_LIBRARIES}的内容链接
target_link_libraries(talker ${catkin_LIBRARIES})
##为目标可执行文件添加依赖
add_dependencies(talker beginner_tutorials_generate_messages_cpp)

add_executable(listener src/listener.cpp)
target_link_libraries(listener ${catkin_LIBRARIES})

add_dependencies(listener beginner_tutorials_generate_messages_cpp)
```
## 后续步骤
* 使用`catkin_make`编译
* 使用`roscore`运行master node 
* `source ./devel/setup.bash`加载环境变量
* `rosrun`运行节点