---
layout:     post
title:      "「原创」增大按钮的点击范围其他不变以及Masonry设置约束后获取控件的frame"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-22 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0005.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 原创
---

> ## 前言  
> 今天在开发的时候需要让一个按钮的点击范围变大,但是不能动他现在的图片和文字的位置  
> 查了一些资料终于成功了

## 首先我们要知道在哪里设置按钮的点击范围  
1. 一般都是建一个基类然后重写下面这个方法  <br />

```
- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent*)event{

CGRect bounds =self.bounds;

//CGFloat widthDelta =44.0- bounds.size.width;

//CGFloat heightDelta =44.0- bounds.size.height;

bounds = CGRectInset(bounds, widthDelta, heightDelta);//注意这里是负数，扩大了之前的bounds的范围

return CGRectContainsPoint(bounds, point);

}
```

**不用关心注释的地方,只要知道`CGRectInset`这个是什么意思就行了  
我特地画了一张图
![img](/img/post-iOS001.jpg)
***代码***

```
    UIView *view1=[[UIView alloc]initWithFrame:CGRectMake(0, 0, 200, 200)];
    
    [view1 setBackgroundColor:[UIColor grayColor]];//view1 设置为灰色
    
    [self.view addSubview:view1];
    
    //根据view1的大小变换后创建view2;
    
    CGRect view2Rect=CGRectInset(view1.bounds, -10, 10);
    
    UIView *view2=[[UIView alloc]initWithFrame:view2Rect];
    
    [view2 setBackgroundColor:[UIColor blueColor]];//view2 设置为蓝色
    
    [self.view addSubview:view2];
```
你可以一直改`CGRect view2Rect=CGRectInset(view1.bounds, -10, 10);` 这段代码中的后面的两个数值就会发现问题所在了  
------
#### 结论
***在`CGRectInset(view1.bounds, widthX, heightY)`方法中,`widthX`,`heightY`为负则使用这个方法的视图的点击范围就会在x和y轴的两个方向正增长,反之就往里面缩小***
------
## 当一个控件用autolayout或者Masonry设置约束后如何获得这个控件的真实的frame
众所周知,当我们用autolayout设置约束后,后面就不能改变这个控件的frame了, 也不能通过修改frame来修改控件的位置.也获取不到当前这个控件真实的frame
但是通过其他方法我们还是可以获得真实的frame的  
***在设置完约束后加个代码`[leftBtn layoutIfNeeded];`***
```
    [headerView addSubview:leftBtn];
    [leftBtn mas_makeConstraints:^(MASConstraintMaker *make) {
        make.centerY.equalTo(headerView);
        make.left.equalTo(headerView).offset(12);
    }];
    [leftBtn layoutIfNeeded];
    NSLog(@"%f-哈哈哈哈哈哈哈哈-%f",rightSwitch.width,leftBtn.tureWidth);
    leftBtn.addX = ET_SCREEN_WIDTH - rightSwitch.width - 3*margin - leftBtn.tureWidth;
    leftBtn.addY = 0;
```
***在这个按钮的基类中***
```
- (void)layoutSubviews {
    [super layoutSubviews];
    
    self.tureWidth = self.width;
    NSLog(@"%@lallalllala",NSStringFromCGRect(self.frame));
}
```
打印之后发现就可以获得了,当然如果还不清楚的,这里有几篇文章写的不错,可以参考一下`注:以下均是'简述'网站`   

------
* [扩大按钮UIButton的点击范围](http://www.jianshu.com/p/30d4b0110e7e)    
* [iOS 开发中关于Frame和约束的简单认识](http://www.jianshu.com/p/59b28de88d7d)
* [iOS 扩大按钮的点击范围](http://www.jianshu.com/p/53a99bca0c49)
* [扩大按钮(UIButton)点击范围(随意方向扩展哦)](http://www.jianshu.com/p/ce2d3191224f)

> #### 结语   
> 非常开心和大家分享我的小小发现,有什么问题都可以在下面给我留言哦!



