---
layout:     post
title:      "「原创」iOS关于tableView的复用"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-26 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0006.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 原创
---

> ## 前言
> `UITableView` 是大家经常使用的一款控件,但是大部分人用的很浅,其中的`cell`的复用机制又是其中最最重要的一点

## 什么事cell的复用(个人理解)
苹果在封装`UITableView`这个控件的时候,就考虑到了视图流畅性和多样性.用过这个控件的都知道一般就是实现以下数据源和代理基本上这个控件就加载完成了.   
但是我们在创建`cell`的时候不知道有么有发现,iOS中,只有cell显示在当前界面,这个cell才会彻底的在内存中创建出来.然后会给这些复用的`cell`一个ID,当视图滑动的时候,消失的cell会被释放,然后放在复用池中,当视图上拉的时候,系统就从复用池中取出对应ID的cell,形成复用,所以不管你有多少个cell,真正在内存中开辟空间的,只有显示在手机屏幕中的那些cell.这样就大大的降低了内存占用,提高流畅性.

## 但是很多时候我们因为产品需要,视图特别复杂的时候,就需要暂时去掉复用

> 我们都知道`tableView`是有重用机制的，它的` tableView`的实现并且不是为每个数据项创建一个`tableCell`。而是只创建屏幕可显示最大个数的`cell`，然后重复使用这些cell，对`cell`做单独的显示配置，来达到既不影响显示效果，又能充分节约内容的目的。
但是需要注意的是正是因为这样的原因：配置`Cell`的时候一定要注意，对取出的重用的`cell`做重新赋值，不要遗留老数据。

## 为了解决cell复用时带有之前的数据问题我下面列出3个方法供大家参考：

1. **重用机制调用的就是`dequeueReusableCellWithIdentifier`这个方法，方法的意思就是“出列可重用的cell”，因而只要将它换为`cellForRowAtIndexPath`（只从要更新的cell的那一行取出cell），就可以不使用重用机制，因而问题就可以得到解决，但会浪费一些空间**
```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath  
{  
    static NSString *CellIdentifier = @"Cell";  
    // UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier]; //改为以下的方法  
    UITableViewCell *cell = [tableView cellForRowAtIndexPath:indexPath]; //根据indexPath准确地取出一行，而不是从cell重用队列中取出  
    if (!cell) {  
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];  
    }  
     //其他代码    
      return cell;                        
}  
```

2. **为每个cell指定不同的重用标识符(`reuseIdentifier`)来解决。重用机制是根据相同的标识符来重用cell的，标识符不同的cell不能彼此重用。**
```
(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath  
{  
      
    NSString *CellIdentifier = [NSString stringWithFormat:@"CellIdentifier%d%d", [indexPath section], [indexPath row]];//以indexPath来唯一确定cell  
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier]; //列出可重用的cell  
    if (cell == nil) {  
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];  
    }  
    //其他代码  
    return cell;  
}
```

3. **除重用cell的所有子视图得到一个没有特殊格式的cell，供其他cell重用。**
```
(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath  
{  
    static NSString *CellIdentifier = @"Cell";  
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier]; //列出可重用的cell  
    if (cell == nil) {  
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];  
    }  
    else  
    {  
        //删除cell的所有子视图  
        while ([cell.contentView.subviews lastObject] != nil)  
        {  
            [(UIView*)[cell.contentView.subviews lastObject] removeFromSuperview];  
        }  
    }  
    //其他代码  
    return cell;  
}
```

## 使用`UITableView`中遇到的其他问题

1. 视图中的控件一层有一层重复加载到同样的位置
将新建控件放在下面的代码段中即可
```
    if (cell == nil) {  
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];  
        //添加控件的操作
    } 
```
如果写在外面那么每次`cell`创建的时候都会新建一个控件加载上去,写在里面只会加载一次

2. 如果`cell`中内容较多尽量使用自定义`cell`来做

3. 如果需要自定义`cell`的高度就需要自定义`cell`加上`UITableView-FDTemplateLayoutCell`这个第三方库即可完美解决   
地址:[自定义cell](https://github.com/forkingdog/UITableView-FDTemplateLayoutCell)

4. 一般情况下`tableView`默认是`UITableViewStylePlain`,这种模式中如果有很多组,那么滑动的时候组头就悬浮在顶端,我们只要把模式改成`UITableViewStyleGrouped`,组头就不会悬浮了,当然有人会说改成group之后组头的高度就没有办法控制了,哈哈,之前也遇到过这个问题就是系统会有两个方法来设置组头和组尾的高度的方法,如下:
```
#pragma mark 组间距
- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section{

    return 16;
}
- (CGFloat)tableView:(UITableView *)tableView heightForFooterInSection:(NSInteger)section{
    return 0.001;
}
```发现没有,我只需要组头,不需要组尾,但是为什么要设置组尾的高度是0.001呢,因为这两个方法你如果返回的是0,是没有效果的,只有设置非0的值才能生效,但是有的时候我们只需要单个的组头或者组尾,就需要吧不需要的那个间距设置成特别小数值就行了!



