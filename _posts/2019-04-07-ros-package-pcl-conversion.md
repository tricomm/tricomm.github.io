---
layout: post
title:  "探索apt-get下载多ros包-pcl_conversion"
date:   2019-04-01 20:37:46
categories: ros pcl apt
tags: pcl 
---
# 探索apt-get下载多ros包-pcl_conversion
## 疑问
1. apt下载的是可执行文件还是源码在编译(猜测是源码因为可以很好的适应不同环境的pc)
2. apt下载安装软件的路径,如何设置
3. ros如何找到依赖的包(靠环境变量\$ROS_ROOT 
/opt/ros/melodic/share/ros
\$ROS_ETC_DIR 
/opt/ros/melodic/etc/ros
\$ROS_PACKAGE_PATH 
/opt/ros/melodic/share
)
4. apt下载的源代码在哪里
5. 如何设置

## 解决
1. [pcl源码下载](https://translate.googleusercontent.com/translate_c?depth=1&rurl=translate.google.com.hk&sl=en&sp=nmt4&tl=zh-CN&u=https://github.com/PointCloudLibrary/pcl/releases&xid=17259,15700023,15700186,15700190,15700253,15700258&usg=ALkJrhg7cUeT2jjzuEUzwMjuBeZ9Oep2_w)
2. [pcl源码安装步骤](https://translate.googleusercontent.com/translate_c?depth=1&rurl=translate.google.com.hk&sl=en&sp=nmt4&tl=zh-CN&u=https://github.com/laboshinl/loam_velodyne/issues/71&xid=17259,15700023,15700186,15700190,15700253,15700258&usg=ALkJrhj_OCDxqWrWGOhlqe9v2y9JS096MQ#)