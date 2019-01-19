---
layout: post
title:  "c++在vscode下环境的配置!"
date:   2019-01-18 23:14:54
categories: vscode环境配置
tags: c++ vscode 
author: Zhang siyu
mathjax: true
---

# 1.打开工程文件夹
目录结构会出现在VS的左侧:

![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/01/18/caidan.png)
# 2.头文件路径配置文件(c_cpp_properties.json)

* 打开控制面板command palette(command+shift+P),选择`C/Cpp: Edit Configuration`将会自动生成`c_cpp_properties.json`在打开目录下.
```json
{
    "configurations": [
        {
            "name": "Mac",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "macFrameworkPath": [
                "/System/Library/Frameworks",
                "/Library/Frameworks"
            ],
            "compilerPath": "/usr/bin/clang++",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "clang-x64"
        }
    ],
    "version": 4
}
```
参数说明
* “includePath”：所有头文件路径
* "compilerPath":编译器执行文件目录,Mac的usr/bin不允许删写,usr/local/bin是放终端中可直接运行的程序的(环境变量指向这里).`(实验中该属性删掉并没有影响,所以说明编译器还是用的text.json里的命令)`
  
# 3.编译用配置文件(task.json)
* 打开控制面板command palette(command+shift+P),选择`Tasks:Configure Tasks`->`Create tasks.json file from templates`->`Other`将会自动生成task.json在打开目录下.

```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "echo",
            "type": "process",
            "command": "g++",
            "args": [
                "${file}",
                "-g",
                "-o",
                "a.out"
    
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```
参数说明
* "label":任务名称
* "type": 执行方式(shell在命令行执行/process直接运行命令)
* "command":编译执行的命令(相对路径在当前目录下)(此处为gcc)
* ”args“:设置command执行命令的参数
  

关于gcc
* -o filename 如果不加该参数则默认Windows下输出a.exe，Linux/MAC下默认a.out
* -g C/C++调试参数(建立符号表、关闭优化以方便调试)

# 4.调试配置文件(launch.json)
*点击虫子->点击设置->C++(GDB/LLDB)将自动在打开目录下生成`launch.json`
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(lldb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/a.out",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "lldb"
        }
    ]
}
```
参数说明
#### configurations中强制属性:
* type - the type of debugger to use for this launch configuration. Every installed debug extension introduces a type: node for the built-in Node debugger, for example, or php and go for the PHP and Go extensions. 如cppdbg、phpdbg
* request - the request type of this launch configuration. Currently, launch(启动) and attach(附加) are supported. (这俩属性不明白)
* name - the reader-friendly name to appear in the Debug launch configuration drop-down.

#### 许多调试器支持属性:
* program - executable or file to run when launching the debugger (可执行文件 如c++ 源程序经过预处理、编译优化、生成目标文件、链接后得到的可执行文件)
* args - arguments passed to the program to debug
* env - environment variables (the value null can be used to "undefine" a variable)(不知道啥用)
* cwd - current working directory for finding dependencies and other files(直接用${workspaceFolder} 替换就好了)
* port - port when attaching to a running process(链接到正在运行的端口)
* console - what kind of console to use, for example, internalConsole, integratedTerminal, or externalTerminal
* stopAtEntry true则停在入口处
  
# 5.command+shift+b 进行build链接
  根据`task.json`对源文件进行预处理、编译优化、生成目标文件、链接最后得到可执行文件
# 6. fn+F5 Start Debugging 
 根据`launch.json`对可执行文件进行调试,可点击行前空白设置断点.