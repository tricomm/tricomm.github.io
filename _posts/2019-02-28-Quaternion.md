---
layout: post
title:  "quaternion四元数在机器人转向中的应用"
date:   2019-02-28 09:02:46
categories: 四元数
tags: 四元数 ros
mathjax: true
---
# 四元数
## 概述
* 四元数是复数的一个延伸,相对于复数的二维空间,代表着一个四维空间.
* 解决了传统方法如欧拉角的万向锁问题
## 基本公式
* $H=R+xi+yj+zk$
* $i^2=j^2=k^2=-1$
* 满足分配率
* 不满足交换率
  <br>$ij=-ji=k$
  <br>$ki=-ik=j$
  <br>$jk=-kj=i$
* 求共轭
 <br>$q=W+X_0i+Y_0j+Z_0k$
 <br>则: $q^{-1}=W-X_0i-Y_0j-Z_0k$
## 描述三维钢体旋转
1. $p=0+X_1i+Y_1j+Z_1k$ 
  <br>$(X_1,Y_1,Z_1)为钢体三维矢量$

2. $(X_0,Y_0,Z_0)为转轴向量$
   <br>$\alpha 为旋转角度$
   <br>则: $q=cos(\alpha /2)+sin(\aleph/2)(X_0i+Y_0j+Z_0k)$
3. $f(p) = qpq^{-1}为旋转后钢体方向向量$
## 旋转符合右手定理
* 大拇指为转轴向量
* 四指握隆方向为旋转正向
  
## 附 Roll横滚 Pitch俯仰 Yaw偏航角
Roll:
![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/02/28/roll.gif)
Pitch:
![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/02/28/pitch.gif)
Yaw:
![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/02/28/yaw.gif)
