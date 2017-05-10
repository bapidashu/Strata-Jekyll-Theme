---
layout:     post
title:      "「iOS」CocoaPods 的安装,更新,使用"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-19 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0003.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 简书
---

> ## 前言
> 做过iOS开发的都知道日常需要用到很多第三方库,但是这些库的管理和更新太麻烦,于是就有了CocoaPods这一款神器,再也不用为我们项目中的第三方库的管理分心了!

## 1.配置CocoaPods 的安装环境

1. 移除现有的Ruby默认源(由于淘宝已经不支持，以下配置需要做修改)
```
gem sources --remove https://rubygems.org/
```
![img](/img/post_cocoapod/cocoapod01.jpg)
配置openssl的时候，需要查看/usr/local/Cellar/openssl路径下对应的文件，例如我的就是1.0.2d_1
2. 使用新的源
```
gem sources -a https://ruby.taobao.org/   //要改成https://gems.ruby-china.org/ 
```
3. 验证新源是否替换成功
```
gem sources -l
```
4. 安装CocoaPods
+ 第一步
```
sudo gem install cocoapods
```
**备注苹果系统升级 OS X EL Capitan 后改为**
```
sudo gem install -n /usr/local/bin cocoapods
```
![img](/img/post_cocoapod/cocoapod02.jpg)
+ 第二步
```
pod setup
```
![img](/img/post_cocoapod/cocoapod03.jpg)
5. 更新gem
```
sudo gem update --system
```
![img](/img/post_cocoapod/cocoapod04.jpg)
到此`cocoapods`安装最新版本完成

## 2.如何使用CocoaPods 
+ 新建一个demo工程
![img](/img/post_cocoapod/cocoapod05.jpg)
+ 在终端转到该工程路径下，创建一个Podfile文件
![img](/img/post_cocoapod/cocoapod06.jpg)
+ 输入`i`进入编辑模式，编辑`Podfile`文件内容
![img](/img/post_cocoapod/cocoapod07.jpg)
+ 按下`esc`退出编辑模式，输入`:wq`保存退出
![img](/img/post_cocoapod/cocoapod08.jpg)
+ 输入`pod install`进行安装
![img](/img/post_cocoapod/cocoapod09.jpg)
+ 集成完毕
![img](/img/post_cocoapod/cocoapod10.jpg)

> ### 注意点
> ![img](/img/post_cocoapod/cocoapod11.jpg)
> ![img](/img/post_cocoapod/cocoapod12.jpg)
> ![img](/img/post_cocoapod/cocoapod13.jpg)

+ 在原先的库中添加新的第三方库<br />
* 使用终端 打开该工程下的Podfile文件 *
![img](/img/post_cocoapod/cocoapod14.jpg)
* 不再是pod install了，是pod update *
![img](/img/post_cocoapod/cocoapod15.jpg)
* 之后更新成功，你就会看到你最新的库了 *
![img](/img/post_cocoapod/cocoapod16.jpg)

> ### 注意点
> 如果你想要搜出确定的库，使用例如 pod search AFNetworking 命令
> ![img](/img/post_cocoapod/cocoapod17.jpg)

## 3.想要删除已有cocoapods集成的库，恢复原先工程
* 1.删除工程文件夹下的`Podfile、Podfile.lock`和`Pods`文件夹
![img](/img/post_cocoapod/cocoapod18.jpg)
* 2.删除`xcworkspace`文件
![img](/img/post_cocoapod/cocoapod19.jpg)
* 3.打开`xcodeproj`文件,删除项目中的`libpods.a`和`Pods.xcconfig`引用
![img](/img/post_cocoapod/cocoapod20.jpg)
* 4.打开Build Phases选项，删除Check Pods Manifest.lock和Copy Pods Resources
![img](/img/post_cocoapod/cocoapod21.jpg)


> ## 结语
> 以上北荣转载自简述作者:[iOS小乔](http://www.jianshu.com/users/f029d92cedc0/latest_articles),非常感谢.










