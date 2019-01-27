---
layout: post
title:  "Variadic Templates (数量不定的模版类型)"
date:   2019-01-26 21:02:46
categories: c++14特性
tags: c++ c++14
---
# Variadic Templates 数量不定的模版
* 实现弱类型编程
* 进行弱类型递归(边界条件为:边界函数)
* sizeof...(args)
* `...`是一个pack包
```c++
    template<typename T,typename... types>//这里是一个模版参数包
    void print(const T& firstArg,const Types)
```
## Variadic Templates function

## Variadic Templates class v

