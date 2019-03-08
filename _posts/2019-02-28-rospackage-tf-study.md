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
* `tf`包中的`tf_echo [reference_frame] [target_frame]`可查看target frame目标坐标系在reference frame参考坐标系中的位置`

## C++ 用订阅器subscribe创建坐标系广播链接
1. 在package下src中加入broadcastername.cpp
```cpp
#include <ros/ros.h>
#include <tf/transform_broadcaster.h> //tf包的TransformBroadcaster帮助简化发布任务
#include <turtlesim/Pose.h>

std::string turtle_name;



void poseCallback(const turtlesim::PoseConstPtr& msg){
  static tf::TransformBroadcaster br; //通过该对象发送转换
  tf::Transform transform;//坐标变换对象
  transform.setOrigin( tf::Vector3(msg->x, msg->y, 0.0) );//将2D龟的坐标信息写入 transform中
  tf::Quaternion q;
  q.setRPY(0, 0, msg->theta); //存入RPY
  transform.setRotation(q);//设置旋转
  br.sendTransform(tf::StampedTransform(transform, ros::Time::now(), "world", turtle_name));
  //1.变换 2.时间戳 3.链接的父框架 4.链接的子框架
}

int main(int argc, char** argv){
  ros::init(argc, argv, "my_tf_broadcaster");
  if (argc != 2){ROS_ERROR("need turtle name as argument"); return -1;};
  turtle_name = argv[1];

  ros::NodeHandle node;
  ros::Subscriber sub = node.subscribe(turtle_name+"/pose", 10, &poseCallback);

  ros::spin();
  return 0;
};
```
2. package的CMakeList.txt添加
```
#编译生成可执行文件
add_executable(turtle_tf_broadcaster src/turtle_tf_broadcaster.cpp)
#链接库设置
target_link_libraries(turtle_tf_broadcaster ${catkin_LIBRARIES})
```
