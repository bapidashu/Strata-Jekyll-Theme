---
layout:     post
title:      "「iOS」CocoaPods 的安装,更新,使用"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-19 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0003.jpg"
header-mask: 0.3
catalog:    true
tags:
    - iOS
    - 软件开发
---

> ## 前言
> 做过iOS开发的都知道日常需要用到很多第三方库,但是这些库的管理和更新太麻烦,于是就有了CocoaPods这一款神器,再也不用为我们项目中的第三方库的管理分心了!

## 1.配置CocoaPods 的安装环境

#### 1. 移除现有的Ruby默认源(由于淘宝已经不支持，以下配置需要做修改)
```
gem sources --remove https://rubygems.org/
```
![img](/img/post_cocoapod/cocoapod01.jpg)