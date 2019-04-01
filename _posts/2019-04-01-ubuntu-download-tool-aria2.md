---
layout: post
title:  "ubuntu下载百度云大文件(aria2+fairfox plugin:百度网盘助手)"
date:   2019-04-01 0:02:46
categories: linux
tags: linux unbuntu
---
# ubuntu下载百度云大文件(aria2+fairfox plugin:百度网盘助手)
## fairfox plugin 网盘助手
* [链接](https://addons.mozilla.org/zh-CN/firefox/addon/baidu-pan-exporter/)
* [git源码](https://github.com/acgotaku/BaiduExporter)
* 安装后重启浏览器,在百度云盘中选中需要下载的文件会弹出导出下载.
## aria2 多线程下载软件
1. 介绍 
  
* aria2 本是命令行下的一个多线程下载工具,属于后端,通过在浏览器中安装插件,将百度网盘中的下载地址导出到aria2中下载,具体有以下优点:多线程下载。aria2可以从通过多种协议或多个源头下载同一文件，这样可以最大化下载带宽。
* 轻量级。 aria2运行时不会占用太多内存与cpu，非常轻量级。当磁盘缓存关闭时，aria2占用物理内存通常为4Mib(正常的HTTP/FTP下载)到9Mib(Bittorrent下载).使用Bittorrent以2.8Mib/sec速度下载，cpu占用率大概为6%。
* 所有的BitTorrent客户端的功能都能使用:DHT,PEX,Encryption,Magnet URI,Web-Seeding,Selective Downloads,Local Peer Discovery 和UDP tracker.
* 远程控制。 aria2运行PRC接口来控制aria2进程。支持JSON-PRC(over HTTP and WebSocket)和XML-PRC两种接口。 

2. 安装

    ```
    sudo apt-get install aria2
    sudo mkdir /etc/aria2    #新建文件夹  
    sudo touch /etc/aria2/aria2.session    #新建session文件
    sudo chmod 777 /etc/aria2/aria2.session    #设置aria2.session可写 
    sudo vim /etc/aria2/aria2.conf    #创建配置文件
    ```
3. 配置
    ```
    #＝＝＝＝＝＝＝＝＝文件保存目录自行修改
    dir=/home/username/Desktop/Downloads #username根据实际情况修改
    disable-ipv6=true

    #打开rpc的目的是为了给web管理端用
    enable-rpc=true
    rpc-allow-origin-all=true
    rpc-listen-all=true
    #rpc-listen-port=6800
    #断点续传
    continue=true
    input-file=/etc/aria2/aria2.session
    save-session=/etc/aria2/aria2.session

    #最大同时下载任务数
    max-concurrent-downloads=20

    save-session-interval=120

    # Http/FTP 相关
    connect-timeout=120
    #lowest-speed-limit=10K
    #同服务器连接数
    max-connection-per-server=10
    #max-file-not-found=2
    #最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要
    min-split-size=10M
    #单文件最大线程数, 路由建议值: 5
    split=10
    check-certificate=false
    #http-no-cache=true
    ```

4. 启动aria2
   ```
   sudo aria2c --conf-path=/etc/aria2/aria2.conf     
   #如果没有提示错误，按ctrl+c停止运行命令，转为后台运行： 
   sudo aria2c --conf-path=/etc/aria2/aria2.conf -D1
   ```
5. aria2 控制台

    打开http://aria2c.com/