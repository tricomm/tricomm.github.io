---
layout: post
title:  "linux-unbuntu学习随笔"
date:   2019-01-26 21:02:46
categories: ROS
tags: ROS
---
# linux-unbuntu 学习随笔
## 基本指令
* **`less` `文件名`**  用lesss打开文件进行以下操作
    
    **\内容1**  搜索内容1
    
    **:q** 退出
* `pwd` 当前路径
* `echo` `[变量/字符串]`输出内容
* `cat [文件]`输出文件内容 
* `cp [复制文件] [目标文件夹]` 复制文件
* `alias 别名="代替名"` 设置别名

    `alias`查看所有别名
    <br>`unalias 别名`去除别名 
* `mkdir [目录名]` 新建目录
## 设备相关指令
* **`lsblk`** 查看磁盘信息,可以看到已挂载设备
    
    设备都在/dev下<br>
    挂载地址为 MOUNTPOINT列内容
* **`umount` `设备地址`** 取消挂载
* **`fdisk` `设备地址`** 磁盘分区管理
* **`dd`** 指定大小拷贝文件
## 脚本相关
* `source fname.bash` 逐条执行fname.bash中保存的命令

    一般一批指令写的文件后缀为.bash
* `export` 设置显示环境变量 
 
  `export PATH=$PATH:<添加的路径>`只在当前终端生效
* `~/.bashrc`文件中可设置当前用户生效的环境变量
  
   export PATH=<你要加入的路径1>:<你要加入的路径2>: ...... :$PATH
* `/etc/profile`中可设置所有用户生效的环境变量
## 进程相关
* `pgrep` `进程名` 查看指定进程的信息
## 网络相关
* `ifconfig` 网络接口设置,可显示或配置网卡
## 开发用指令
 * make

    用来编译shall脚本程序 ; 在c/c++开发中对makefile进行编译
 *  
## 名词缩写
* PPA personal package archives 个人软件包文档

    所有的ppa都在[launchpad.net](launchpad.net)中
* PID Process Identification 进程标识
* 许可协议,常见的开源许可协议有BSD、MIT、Boost Software License、GPLv2、GPLv3、LGPLv2.1和LGPLv3