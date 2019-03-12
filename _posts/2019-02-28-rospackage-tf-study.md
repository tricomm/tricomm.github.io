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
  
## tf应用 
* 在许多与pose姿态有关的消息类型中.hearer.frame_id可填写对应的tf坐标系

## C++ 用订阅器subscribe创建坐标系广播链接broadcaster
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
## 创建固定坐标系
1. 在package下src中加入name.cpp## 创建新的坐标系
  * 新坐标系必须是已存在某个坐标系的子坐标系,坐标系的父子结构是棵树没有闭环.
```cpp
#include <ros/ros.h>
#include <tf/transform_broadcaster.h>

int main(int argc, char** argv){
  ros::init(argc, argv, "my_tf_broadcaster");
  ros::NodeHandle node;

  tf::TransformBroadcaster br;
  tf::Transform transform;

  ros::Rate rate(10.0);
  while (node.ok()){//设置child frame 到 parent frame的 transform  需要循环发送信息
    //transform.setOrigin( tf::Vector3(0.0, 2.0, 0.0) );
    transform.setOrigin( tf::Vector3(2.0*sin(ros::Time::now().toSec()), 2.0*cos(ros::Time::now().toSec()), 0.0) );
    transform.setRotation( tf::Quaternion(0, 0, 0, 1) );
    br.sendTransform(tf::StampedTransform(transform, ros::Time::now(), "turtle1", "carrot1"));
    rate.sleep();
  }
  return 0;
};
```
## 创建监听器 以及 监听器时间旅行
```cpp
#include <ros/ros.h>
#include <tf/transform_listener.h>//为简化接收任务
#include <geometry_msgs/Twist.h>
#include <turtlesim/Spawn.h>

int main(int argc, char** argv){
  ros::init(argc, argv, "my_tf_listener");

  ros::NodeHandle node;

  ros::service::waitForService("spawn");
  ros::ServiceClient add_turtle =
    node.serviceClient<turtlesim::Spawn>("spawn");
  turtlesim::Spawn srv;
  add_turtle.call(srv);

  ros::Publisher turtle_vel =
    node.advertise<geometry_msgs::Twist>("turtle2/cmd_vel", 10);

  tf::TransformListener listener;//监听器变量需要缓冲10秒左右,所以要让它持久化
  ros::Rate rate(10.0);
  while (node.ok()){
    tf::StampedTransform transform;
    ros::Duration(1.0).sleep();
  // try{
  //    ros::Time now = ros::Time::now();
  //    listener.waitForTransform("/turtle2","/turtle1",now,ros::Duration(3.0));
  //       //1.目标坐标系 2.源坐标系 3.转换时间 4.结果保存对象 目标坐标系是参考系
  //    listener.lookupTransform("/turtle2", "/turtle1",
  //                              now, transform);//ros::Time(0)是leatest available
  //   std::cout<<transform.getOrigin().x()<<" "<<transform.getOrigin().y()<<std::endl;
  //   } //transform进入buffer需要几毫秒的时间 所以不能请求now()
  // try{
  //   ros::Time past = ros::Time::now() - ros::Duration(5.0);
  //   listener.waitForTransform("/turtle2","/turtle1",past,ros::Duration(1.0));
  //   listener.lookupTransform("/turtle2","/turtle1",past,transform);
  //   //两只龟五秒前位置
  // }
  try{
    ros::Time now = ros::Time::now();
    ros::Time past = now - ros::Duration(5.0);
    listener.waitForTransform("/turtle2", now,
                              "/turtle1", past,
                              "/world", ros::Duration(1.0));
    listener.lookupTransform("/turtle2", now,
                             "/turtle1", past,
                             "/world", transform);
  //1.参考坐标系,2.参考系时间,3.源目标系,4.源目标系时间,5.位置不随时间变化的坐标系,6.存储结果变量
  }
    catch (tf::TransformException &ex) {
      ROS_ERROR("%s",ex.what());
      ros::Duration(1.0).sleep();
      continue;
    }
    geometry_msgs::Twist vel_msg;
    vel_msg.angular.z = 1 * atan2(transform.getOrigin().y(),
                                    transform.getOrigin().x());
    vel_msg.linear.x = 0.5 * sqrt(pow(transform.getOrigin().x(), 2) +
                                  pow(transform.getOrigin().y(), 2));//越远越快
    turtle_vel.publish(vel_msg);

    rate.sleep();
  }
  return 0;
};

```