---
layout: post
title:  "iOS开发中模拟慢速网络工具Network Link Conditioner的使用"
date:   2016-04-21 11:23:32
categories: iOS 
---
最近项目中遇到了很多慢速网络下的bug，为了方便测试，苹果提供了相当好用的网速模拟工具--Network Link Conditioner，使用超级简单。

第一步 -- 下载
  打开Xcode - 选择 Xcode -> Open Developer Tool -> More Develop Tools。用苹果账号登录网站，搜索Hardware IO Tools 下载Xcode对应版本的工具。
![下载](http://upload-images.jianshu.io/upload_images/734694-eb3808782a56c1fd.png)



第二步 -- 安装 
打开下载的文件，双击其中的 Network Link Conditioner 安装。
![安装](http://upload-images.jianshu.io/upload_images/734694-2985b5ec06b95ddc.png)

安装完成后，工具会在系统设置中的最后一排出现，

![工具](http://upload-images.jianshu.io/upload_images/734694-6256675f10608b70.png)

使用：
使用非常简单，看一眼就能会，这些配置不能满足需求的话，可以自行配置网络方案，建议模拟慢网速选择Edge模式。开启之后会影响当前mac的网络速度。

tips: 

真机调试时模拟网速更加方便，在iPhone设置里，选择开发者->Status 这个就是网速模拟，选择好模式记得选择Enable打开。

![IMG_1673.jpg](http://upload-images.jianshu.io/upload_images/734694-00063cd47aa79e0c.jpg)

![IMG_1673.jpg](http://upload-images.jianshu.io/upload_images/734694-f91970ebb3cb4b7f.jpg)
