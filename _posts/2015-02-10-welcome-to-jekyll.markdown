---
layout: post
title:  "使用hekyll+github写博客步骤 "
date:   2019-01-9 15:14:54
categories: jekyll
tags: jekyll git 
---

## 1.同步github远程仓库与本地
* 本地有修改则commit到本地master然后push到git  
  例：新建了一个文件`new.txt` 
  ```
  cd 上层文件夹 
  git add new.txt           //将new.txt添加到仓库
  git commit -m备注内容      //将更新提交到仓库并备注
  git push -u origin master //将本地master分支推送到愿从仓库
                            //origin 的master分支
                            //-u 参数是设置origin为默认主机
  git push                  //已有默认主机则推送到默认主机
  git push origin master    //一般情况
  ```
* github远
* 程仓库有改动
  ```
  git pull origin master//把远程仓库origin的master抓下来
  ```

## 2.在`_posts`文件夹下新建`博客名.md`文件作为新的blog

## 3.`博客名.md`中头部标注格式
    ---                                   //头部标注分隔符
    layout: post                          //对应_layouts文件夹内容
    title:  "hello jekyll! "
    date:   2015-02-10 11:40:18 +0800
    categories: jekyll                //内容分类显示在主页及博客缩略图
    tags: jekyll 端口 markdown         //可有多个标签用空格隔开
    author: Haoyang Gao                //(可选)
    mathjax: true                      //(数学)
    ---

头部格式设置完毕后会显示为:![](https://raw.githubusercontent.com/tricomm/ImageForBlog/master/2019/01/09/postheadinhome.png)

## 4.以markdown语法写博客内容
## 5.与远程仓库同步