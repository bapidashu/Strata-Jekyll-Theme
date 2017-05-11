---
layout:     post
title:      "「iOS」iOS如何将父视图透明，而内容不透明的方法"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-21 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0004.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
---

> **前言**  <br />
> 相信很多人在设置某个视图透明的时候会发现这个视图上面的子视图也变成透明的了,从而达不到自己想要的效果
> 下面就是怎么设置父视图透明而子视图不透明的方法

#### 先看一下错误的做法
```
self.view.backgroundColor = [UIColor clearColor];
self.view.alpha = 0.5;
```
这样写虽然可以达到透明的效果，往往也会造成添加改self.view视图上面的所有子视图的会产生透明，然而这往往是我们不需要的。

#### 正确的做法
```
self.view.backgroundColor = [[UIColor whiteColor]colorWithAlphaComponent:0.7f];
```
我们只设置了背景是透明的，没有全局的设置view的透明属性，<br>就能使得添加到view的所有子试图保持原来的属性，不会变成透明的

😝啦啦啦😋是不是很激动呢?嘿嘿
