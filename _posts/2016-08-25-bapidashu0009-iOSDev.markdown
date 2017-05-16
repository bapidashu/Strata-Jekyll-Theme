---
layout:     post
title:      "「简书」iOS中简单的画线功能"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-25 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0005.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 简书
---

> 最近在iOS开发中，需要使用iOS的画线功能，画线的方法可以写在一个`Controller`视图中，当然这不是最好的方式，建议还是自定义一个`UIView`，并重写`drawRect:`方法，这样后面方便使用，并且不会造成代码的冗长与啰嗦。

## 1.新建一个类，继承自UIView  <br />
重写drawRect:方法：
```
- (void)drawRect:(CGRect)rect {

    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextSetLineCap(context, kCGLineCapRound);
    CGContextSetLineWidth(context, 3);  //线宽
    CGContextSetAllowsAntialiasing(context, true);
    CGContextSetRGBStrokeColor(context, 70.0 / 255.0, 241.0 / 255.0, 241.0 / 255.0, 1.0);  //线的颜色
    CGContextBeginPath(context);

    CGContextMoveToPoint(context, 0, 0);  //起点坐标
    CGContextAddLineToPoint(context, self.frame.size.width, self.frame.size.height);   //终点坐标

    CGContextStrokePath(context);
}
```   

## 2.在其他类中调用
```
- (void)viewDidLoad {
    [super viewDidLoad];

    CustomLine *line = [[CustomLine alloc] init];
    line.backgroundColor = [UIColor whiteColor];
    line.frame = self.view.frame;
    [self.view addSubview:line];
}
```

## 3.需要注意的问题：
在这里直接运行，就会出现画的线段，但是我在项目中写的时候，发现画线并没有出现（项目使用的是`swift`），说明系统没有自动的调用`drawRect:`方法，这里就需要我们在`Controller`视图中手动的调用`[line setNeedsDisplay];` 这句话是手动的让系统去调用`drawRect:`方法。  <br />

**注意：不要试图手动去调用drawRect:方法，因为这是系统负责调用的。**