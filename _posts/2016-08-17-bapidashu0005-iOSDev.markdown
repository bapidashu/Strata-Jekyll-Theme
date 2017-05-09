---
layout:     post
title:      "「iOS」Objective-C页面消失或出现时，判断是pop还是push操作"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-17 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0003.jpg"
header-mask: 0.3
catalog:    true
tags:
    - iOS
    - CSDN
    - 软件开发
---

> ## 前言 
> 有的时候我们需要在这个界面push进来或者pop出去的时候做一些判断和操作,但是系统并没有给出直接的方法让我们去判断是push还是pop,所以这时候我们就需要用下面的方法去判断

### 我们先要了解在控制器消失之前会调用的两个方法
```C
- (void)viewWillDisappear:(BOOL)animated;
-(void)viewDidDisappear:(BOOL)animated;
```
这两个方法；在这两个方法中进行判断消失的方式即可：
```C
- (void)viewWillDisappear:(BOOL)animated {
    NSArray *viewControllers = self.navigationController.viewControllers;//获取当前的视图控制其
    if (viewControllers.count > 1 && [viewControllers objectAtIndex:viewControllers.count-2] == self) {
        //当前视图控制器在栈中，故为push操作
        NSLog(@"push");
    } else if ([viewControllers indexOfObject:self] == NSNotFound) {
        //当前视图控制器不在栈中，故为pop操作
        NSLog(@"pop");
    }
}
```
### 页面出现时会先后执行：
```C
-(void)viewWillAppear:(BOOL)animated
-(void)viewDidAppear:(BOOL)animated
```
### 这两个方法；如果是push出来的页面则还会执行：
```C
- (void)viewDidLoad
```
### 所以可在viewdidload里加个 isPush = YES布尔值,然后在Appear中根据isPush判断是push还是pop：
```Object-C
-(void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    if (_isPush) {
        //push
    }else{
        //pop
    }
}
```

### 别忘了页面消失时置isPush为NO:
```C
- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    _isPush = NO;
}
```
> ### 结语
>  希望能帮助大家,本文转载自[CSDN](http://www.csdn.net/?ref=toolbar),作者:[EverStar's Blog](http://my.csdn.net/liu1347508335)
