---
layout: post
title:  "LOAM论文学习"
date:   2019-03-05 21:02:46
categories: tf
tags: ROS tf
mathjax: true
---
# LOAM: Lidar Odometry and Mapping in Real-time
## 1.introduction
* LOAM分为两个算法:
  1. Odometry:高频低保真用来估计速度
  2. Mapping:低频高保真用来精确匹配以及定位
* 假设以及前置条件
  1. 扫描速度>>运动速度时 失真可以忽视
  2. lidar校准准确
  3. lidar角速度及线速度平滑连续
* 定义
  1. $k,k\in Z^+$表示某次扫描
  2. $P_k$表示第k次扫描生成的点云
  3. $\{L\}$表示一个三维坐标系,坐标系的$(0,0,0)$为lidar的几何中心
  4. $X^L_{(k,i)}$表示在$\{L_k\}$中的点$i\in P_k$的坐标coordinates
  5. $\{W\}$表示一个三维坐标系,坐标系的$(0,0,0)$为lidar的初始位置
  6. $X^W_{(k,i)}$表示在$\{W_k\}$中的点$i\in P_k$的坐标coordinates
  7. P^ 是laser每次扫描映射到$\{L\}$中的点
  8. CW顺时针,CCW逆时针
  9. $\epsilon_{k}$为k时的边缘点集合
  10. $H_{k}$为k时的平面点集合
## 2.overview
![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/05/loam_baseframe.png)
* Point Cloud Registration :第k次完整扫描sweep
* LidarOdometry : 计算lidar两次扫描,以矫正运动带来的$P_k$失真,算法以10Hz左右的频率运行.再将校正后的$P_k$以1Hz的频率输出
* Lidar Mapping 绘制地图
* Transform Integration :将来嗯个算法的P进行变换积分,生成10HZ的{W}信息.
## 3.Lidar Odometry
### a.特征点提取
* 实验使用的激光雷达以$180^◦/s$的角速度旋转,扫描频率为40HZ,因此垂直于扫描平面的分辨率为$180^◦/40=4.5^ ◦$,一次扫描有着共面关系
* 特征点选择边缘与平面上的,$i\in P_k$,$S$是一次扫描返回的i的连续集合,$S$在$i$的每一侧包含一半的点.
* 平滑度计算公式
  <br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/05/c.png)
  <br>周围多个点坐标(x,y,z)求和在比上总点数乘i点坐标.
  <br>c越接近1表示越平滑.大于1突出比平均水平,小于1凹陷比平价水平
  <br>c最大的点作为边缘点
  <br>c最小的作为平面点
  <br>平面分为四个区域
  <br>每个区域最多提供2个边缘点和4个平面点
  <br>只有超过某个阀值才进行选择
* 不希望得到的点:
    1. 被包围的点(选择的点周围点不能在被选中)
    2. 与激光素大致平行表面上的点. (a)图B点
    3. 遮挡区域边界上的点(某些视角可见某些不可见) (b)图A点
   <br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/05/choice_point.png)
    4. 每个区域不能超过边缘点/平面点最大值
* $P_k$被重新投影到时间戳$t_{k+1}$下记为$\vec{P_k}$在K+1期间与$P_{k+1}$一同估计激光雷达运动
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/05/Ptime.png)
