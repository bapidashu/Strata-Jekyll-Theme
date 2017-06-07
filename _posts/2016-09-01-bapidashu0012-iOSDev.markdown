---
layout:     post
title:      "「原创」iOS关于重叠视图下获取点击的视图"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-09-01 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0007.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 原创
---

> ## 前言 
> 在开发中我们可能会碰到这样的情况,在一个父视图中有多个子视图,我们需要精准的获取到当前点击的点是哪个视图   
> 在这个方法中
> `- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event`

**我们需要的效果,类似于这样,这是从网上down的图**
![gif](/img/iOSDev-touchPoint.gif)
**正常情况下我们只需要说点击父视图中的某个视图才执行我们需要的方法**但是在上面这个方法中我们不管点击的是子视图还是父视图都会执行,所以我们要将其区分开来
1. 首先获取到当前点击的点
```
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    
    CGPoint point = [[touches anyObject] locationInView:self.view];

}
```
2. 获取到子视图和父视图重叠的点
```
point = [self.mainTableView.layer convertPoint:point fromLayer:self.view.layer];
```
3. 判断这个点如果在子视图上就执行我们想要的效果,如果这个子视图没有包含这个重叠的点,就是点击在子视图之外的地方
```
if ([self.mainTableView.layer containsPoint:point]) {
    NSLog(@"不做任何处理");
}else {
    NSLog(@"退出");
    [self dismissViewControllerAnimated:YES completion:nil];
}
```
4. 这样我们在应用中,如果需要判断点击父视图执行某个事件,点击子视图啥都不执行的话,就可以这样做
------
5. 简便方法
```
CGPoint point=[[touches anyObject]locationInView:self.view];


CALayer *layer=[whiteView.layer hitTest:point];
if (layer==whiteView.layer) {
    UIAlertView *alertView=[[UIAlertView alloc]initWithTitle:@"" message:@"Inside white layer" delegate:nil cancelButtonTitle:@"OK" otherButtonTitles: nil];
    [alertView show];
}
else if (layer==blueLayer){
    UIAlertView *alertView=[[UIAlertView alloc]initWithTitle:@"" message:@"Inside blue layer" delegate:nil cancelButtonTitle:@"OK" otherButtonTitles: nil];
    [alertView show];
}
```


> ## 结语
> 希望能帮助大家,有需要可以给我留言




