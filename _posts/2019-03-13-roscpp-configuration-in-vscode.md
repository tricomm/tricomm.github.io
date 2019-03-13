---
layout: post
title:  "ros项目调试:vscode下配置开发ROS项目"
date:   2019-03-13 21:02:46
categories: vscode ROS C++
tags: ROS
mathjax: false
---
# ros项目调试:vscode下配置开发ROS项目
## c_cpp_properties.json配置
1. 使用ROS的`catkin_make`编译并输出编译信息文件
    ```
    catkin_make -DCMAKE_EXPORT_COMPILE_COMMANDS=Yes
    ```   
2. 在configurations变量组中添加
    ```json
    "compileCommands": "${workspaceFolder}/build/compile_commands.json"
    ```
    令vscode,读取编译信息文件

c_cpp_properties.json如下
```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "clang-x64",
            "compileCommands": "${workspaceFolder}/build/compile_commands.json"
        }
    ],

    "version": 4
}
```

## catkin_make编译设置
   1. 借助Task功能,Ctrl+shift+P进入命令模式，键入tasks: Configure Task生成task.json
```json
    {
    "version": "2.0.0",
    "tasks": [
        {
            "label": "catkin_make", //代表提示的描述性信息
            "type": "shell",  //可以选择shell或者process,如果是shell代码是在shell里面运行一个命令，如果是process代表作为一个进程来运行
            "command": "catkin_make",//这个是我们需要运行的命令
            "args": [],//如果需要在命令后面加一些后缀，可以写在这里，比如-DCATKIN_WHITELIST_PACKAGES=“pac1;pac2”
            "group": {"kind":"build","isDefault":true},
            "presentation": {
                "reveal": "always"//可选always或者silence，代表是否输出信息
            },
            "problemMatcher": "$msCompile"
        },
    ]
}

```
其中
```json
"group": {"kind":"build","isDefault":true},
```
代表将我们定义的这个task添加到build组里面，这样就可以中Ctrl+Shift+B快捷键来找到编译命令，命令名称就是在label里面定义的，如果"isDefault":true那么就代表直接执行command，如果为false还需要在build下拉里面选一下，我们这里就是label名字：catkin_make

## GDB debug配置
1. 生成launch.json文件
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch", // 配置名称，将会在调试配置下拉列表中显示
            "type": "cppdbg",  // 调试器类型 该值自动生成
            "request": "launch",  // 调试方式,还可以选择attach
            "program": "${workspaceRoot}/devel/lib/waypoint_follower/pure_persuit", //要调试的程序（完整路径，支持相对路径）
            "args": [],  // 传递给上面程序的参数，没有参数留空即可
            "stopAtEntry": false,  // 是否停在程序入口点（停在main函数开始）
            "cwd": "${workspaceRoot}",  // 调试程序时的工作目录
            "environment": [], //针对调试的程序，要添加到环境中的环境变量. 例如: [ { "name": "squid", "value": "clam" } ]
            "externalConsole": false,   //如果设置为true，则为应用程序启动外部控制台。 如果为false，则不会启动控制台，并使用VS Code的内置调试控制台。
            "MIMode": "gdb",  // VSCode要使用的调试工具
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```
"program"参数为要测试节点对应的可执行文件

需要额外终端运行roscore,来运行ROS MASTER节点

"request"参数 launch为启动一个node执行代码,同时可以在vscode中断点调试. attach是执行监听任务

可以在调试窗口中使用GDB命令

## ROS插件
1.就叫ROS安装就好
2.直接右键文件夹可creat catkin package
3.(Ctrl+Shift+P) 中可使用`ros::showMasterStatus`