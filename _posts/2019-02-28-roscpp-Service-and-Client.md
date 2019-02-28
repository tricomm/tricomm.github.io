---
layout: post
title:  "ros的cpp库使用建立ROS服务与客户端"
date:   2019-01-26 21:02:46
categories: ROSCpp
tags: ROS C++ ROS库
---
service服务节点
```cpp
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"//自定义的srv生成的头文件

bool add(beginner_tutorials::AddTwoInts::Request  &req, //提供ros服务从Request读入,结果装入Response中
         beginner_tutorials::AddTwoInts::Response &res) //这些数据结构都在AddTwoInts.h中自动生成
{                                                       
  res.sum = req.a + req.b;
  ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
  ROS_INFO("sending back response: [%ld]", (long int)res.sum);
  return true;
}

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_server");
  ros::NodeHandle n;

  ros::ServiceServer service = n.advertiseService("add_two_ints", add);//服务名为add_two_ints
  ROS_INFO("Ready to add two ints.");
  ros::spin();

  return 0;
```
Client节点
```cpp
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"
#include <cstdlib>

int main(int argc, char **argv)//argc-1为参数个数
{
  ros::init(argc, argv, "add_two_ints_client");
  if (argc != 3)
  {
    ROS_INFO("usage: add_two_ints_client X Y");
    return 1;
  }

  ros::NodeHandle n;
  ros::ServiceClient client = n.serviceClient<beginner_tutorials::AddTwoInts>("add_two_ints");//为add_two_ints服务创建client
  beginner_tutorials::AddTwoInts srv;//创建服务数据类型
  srv.request.a = atoll(argv[1]);//写入request信息
  srv.request.b = atoll(argv[2]);
  if (client.call(srv))//调用成功则输出response信息
  {
    ROS_INFO("Sum: %ld", (long int)srv.response.sum);
  }
  else
  {
    ROS_ERROR("Failed to call service add_two_ints");
    return 1;
  }

  return 0;
}
```
## 后续步骤
1. 在CMakeLists.txt文件中加入
```
#创建可执行程序
add_executable(add_two_ints_server src/add_two_ints_server.cpp)
#链接
target_link_libraries(add_two_ints_server ${catkin_LIBRARIES})
#添加依赖
add_dependencies(add_two_ints_server beginner_tutorials_gencpp)

add_executable(add_two_ints_client src/add_two_ints_client.cpp)
target_link_libraries(add_two_ints_client ${catkin_LIBRARIES})
add_dependencies(add_two_ints_client beginner_tutorials_gencpp)
```