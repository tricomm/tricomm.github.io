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
* 术语
  1. 6-DOF: 6自由度
  2. KD-tree K维树用来组织表示 K 维空间中点集合,它是一种带有其他约束条件的二分查找树。
  3. skew symmetric matrix 斜对称矩阵
  4. diag()对角阵
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

* 平滑度计算公式(1)
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
    1. 每个区域不能超过边缘点/平面点最大值
### B.Finding Feature Point Correspondence 找特征点关系

* $P_k$被重新投影到时间戳$t_{k+1}$下记为$\vec{P_k}$在K+1期间与$P_{k+1}$一同估计激光雷达运动
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/05/Ptime.png)
<br>找$\vec{P_k}$与$\epsilon_{k+1}$和$H_{k+1}$的对应关系
<br>$t_{k+1}$时$P_{k+1}$是空集

* 第K+1次迭代的开始会利用第K次的6-DOF运动估计将$\epsilon_{k+1}$和$H_{k+1}$重新投影为$\vec{\epsilon_{k+1}}$和$\vec{H_{k+1}}$(重投影集)
<br>$\vec{\epsilon_{k+1}}$和$\vec{H_{k+1}}$(重投影集)中的每一个点将在$\vec{P_k}$寻找附近的点
<br>注:$\vec{P_k}$存在一个3d-tree内以便快速查找

* the procedure of finding an edge line as the correspondence of an edge point(特征点) in $\vec{\epsilon_{k+1}}$(a).
    1. $i\in \vec{\epsilon_{k+1}}$ ,$j\in \vec{P_k
}$ , $j$是$i$的最临近点
    2.  let l be the closest neighbor of i in the two consecutive scans to the scan of j.(j, l)`(边缘线edge line)` forms the correspondence of i`(边缘点edge point)`. 
    3. 前置条件: scan cannot contain more than one points from the same edge line.
    4. 验证:通过(1)式来验证j,l是否为边缘点
* the procedure of finding a planar patch as the correspondence for a planar point in ̃$\vec{H_{k+1}}$(b).
  1. $i\in \vec{H_{k+1}}$ ,$j\in \vec{P_k
}$ , $j$是$i$的最临近点,与procedure of finding an edge line 相同.
  2. $l\in \vec{P_k}$ 在与j的同一次扫描中
  3. let m be the closest neighbor of i in the two consecutive scans to the scan of j
  4. 验证:通过(1)式来验证点i,j,m的共面性

    <br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/edge_line.png)
    <br>注:桔黄色线代表对j的一次扫描,蓝色线代表两次连续扫描
* 计算特征点[i](edge point)到对应的edge line[j,l]的距离
  距离$d\epsilon$满足下(2)式,将通过最小化最小化特征点总距离来恢复激光雷达运动
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p2.png)
<br>分子为$\vec{ji},\vec{li}$构成平行四边型面积,是三角形$ijl$的两倍.
* 点到平面距离满足下(3)式
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p3.png)
<br>分子为$\vec{ji},\vec{lj},\vec{mj}$构成平行六面体体积.分母为底面积,二者之比为点$i$到平面$lmj$距离

### C.Motion Estimation移动估计
* 定义
  1. 记 $t$ 为当前时间, $t_{k+1}$ 为第k+1次扫描开始
  2. $T^L_{k+1}=[t_x,t_y,t_z,\theta_x,\theta_y,\theta_z]^T$ is lidar pose transform between$[t_{k+1},t]$,$T^L_{k+1}$是6-DOF六自由度的刚性运动在
  3. $t_x,t_y,t_z$是在$\{L\}$中沿着$x,y,z$ axes轴的translation平移
  4. $\theta_x,\theta_y,\theta_z$是旋转角度,符合右手定则
  5. $i,i\in P_{k+1}$ , $t_i$是i的时间戳
  6. $T^L_{(k+1,i)}$be  the  pose  transform  between$[t_{k+1}, t_i]$
  7. $\Epsilon_{k+1},H_{k+1}$是从$P_{k+1}$中提取的边缘点及平面点集合
  8. $\vec{\Epsilon_{k+1}},\vec{H_{k+1}}$是重新投影到开始时间戳,如[t,t+1]扫描完了投影到t时间点

* $T^L_{(k+1,i)}$可以通过$T^L_{k+1}的线性插值来计算,公式(4)如下:
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p4.png)
* 应用(4)可以建立$\Epsilon_{k+1},H_{k+1}$到$\vec{\Epsilon_{k+1}},\vec{H_{k+1}}$的几何变换公式(5)如下:
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p5.png)
<br>注:
  1. $X^L_{(k+1,i)}$是$i$在$\Epsilon_{k+1}$或$H_{k+1}$中的坐标 , $\vec{X^L_{(k+1,i)}}$是对应点在$\vec{\Epsilon_{k+1}},\vec{H_{k+1}}$中的坐标
  2. $i$是过程中的一个点
  3. $T^L_{k+1}(1:3)$是指$T^L_{k+1}$矩阵的前三行
  4. R是Rodrigues公式定义的旋转矩阵
    <br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p678.png)
  5. $\theta$是旋转的大小,是旋转向量的绝对长度
  6. $\omega$是旋转的单位方向
  7. $\vec{\omega}$是$\omega$的斜对称矩阵
* 结合(2)和(4)-(8)可以得到边缘点与边缘线之间的关系
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p9.png)
<br>通过k+1次扫描中的一个特征点i,k+1到现在的位置改变,得到边缘点到线的距离
* 结合(3)和(4)-(8)可以得到平面点与平面之间的关系
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p10.png)
<br>通过k+i次扫描中的一个平面点i,以及k+1次扫描到现在的位置改变,获得i到其对应平面的距离
* 结合(9),(10)可以得到代价函数:
<br>![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/03/06/p11.png)
位置变换的代价函数,可以验证位置变换的正确性
* 计算$J=\partial f/\partial T^L_{k+1}$ 通过非线性最小二乘法,将d下降到0
* $J$为列向量$J^TJ$为一个数