---
layout: post
title:  "const、&Reference引用与*pointer指针 "
date:   2019-01-26 21:02:46
categories: c++特性
tags: c++ Pointer Reference const
---
# 变量修饰中的const、&、*的适用范围
## 1、const限定符
* 在定义变量时,我们希望这个变量不会改变则使用const type

    `const变量定义时必须初始化`
* 在定义函数参数时使用const修饰x,则在函数体内不可修改x.
## 2、变量variable
variable的定义，当你申明一个变量的时候，计算机会将指定的`一块内存空间和变量名进行绑定`,也就是说int x = 5;说“x等于5”是错误的,应该说`将5赋值于名字叫做x内存地址为0x001的内存空间`.

![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/01/26/value.jpg)
## 2、pointer指针
* 指针也是一个变量,只是`其中存储的值为一个地址`,比如ptr的值就是名称为x的内存空间的地址
* 当要获取指针变量内的值指向地址的值的时候需要用到`dereference解引用`即`*ptr`.
![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/01/26/pointer.jpg)

## 3、Reference引用&即是一个别名是一种特殊的Pointer

* reference引用是一种特殊的pointer,它的内存结构与pointer相同,但是使用方法有所不同,按照C++Primer中的说法`Reference即是一个别名`.

* 具体来说有以下三点区别
        
    1、y作为别名reference不需要`dereference解引用`操作即可获取名为y的内存空间中的地址对应的内存空间的值.

    2、y作为别名,必须要初始化,在定义时时就与一个变量绑定,且生命周期中不可改变.而pointer可以灵活的重新指向新的空间. 

    3、y作为别名在初始化(赋值)时无需去地址如`int &y=x;`,而pointer赋值时需要取地址如`int* ptr= &x;`

![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/01/26/reference.jpg)


