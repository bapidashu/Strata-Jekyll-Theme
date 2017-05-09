---
layout:     post
title:      "「原创」iOS中UITableView使用细节"
subtitle:   "让我们能更好的去使用UITableView"
date:       2016-08-10 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0002.jpg"
header-mask: 0.3
catalog:    true
tags:
    - iOS
    - 原创
    - 软件开发
---

> ##前言
> UITableView 使我们在iOS开发中最常用的视图控件之一,苹果对其进行了非常好的封装,我们在日常中也经常使用,但是也有一些细节是很多人容易忽略的

## 关于预估行高
```
self.tableView.estimatedRowHeight = 121;
self.tableView.rowHeight = UITableViewAutomaticDimension;
```
注意：要使cell上的约束能充满整个cell，这样预估行高才有作用。(一般我们使用预估行高的时候,应该就是我们的cell中的内容的高度不确定的时候)

## 关于注册cell
```Object-C
[self.tableView registerNib:[UINib nibWithNibName:@"TableViewCell" bundle:nil] forCellReuseIdentifier:@"cell"];
```
当我们用xib注册cell的时候,在cell的自定义类中,初始化方法会走
```Object-C
- (void)awakeFromNib {

             [super awakeFromNib];

            // Initialization code

}
```
这个方法

在`cellForRowAtIndexPath`中使用`dequeueReuseableCellWithIdentifier:forIndexPath:`获取重用的cell，若无重用的cell，将自动使用所提供的nib文件创建cell并返回（若使用旧式`dequeueReuseableCellWithIdentifier:`方法需要判断返回是否为空而进行新建）；

## 注册号cell方法中的区别
1. `dequeueReuseableCellWithIdentifier:`
2. `dequeueReuseableCellWithIdentifier:forIndexPath：`
前不必向`tableView`注册`cell`的`Identifier`，但需要判断获取的cell是否为`nil`；
后则必须向`table`注册`cell`，可省略判断获取的`cell`是否为空，因为无可复用`cell`时`runtime`将使用注册时提供的资源去新建一个`cell`并返回

## 自定义cell添加到视图中的细节
自定义cell时，记得将其他内容加到`self.contentView`上，而不是直接添加到` cell `本身上

> ## 总结
> 1.自定义cell时，
若使用nib，使用 `registerNib:` 注册，dequeue时会调用 cell 的 `-(void)awakeFromNib`
不使用nib，使用 `registerClass:` 注册, dequeue时会调用 cell 的 `- (id)initWithStyle:withReuseableCellIdentifier:`
> </br>
> 2.需不需要注册？
使用`dequeueReuseableCellWithIdentifier:`可不注册，但是必须对获取回来的cell进行判断是否为空，若空则手动创建新的cell；
使用`dequeueReuseableCellWithIdentifier:forIndexPath:`必须注册，但返回的cell可省略空值判断的步骤。



