---
layout:     post
title:      "「简书」iOS中计算文件大小两种方法"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-09-04 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0009.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 原创
---

> ## 计算文件或者文件夹的占用内存空间的大小 


+ 场景需求:给一个文件,或者文件夹,计算出这个文件或者文件夹的大小(字符串);
+ 分析:
+ 文件的操作需要用到文件管理者NSFileManager这个类来操作
+ 无论是文件还是文件夹都必须找到它的全路径(而且是绝对路径),这样才能根据路径找到它
+ 如果是文件夹,需要层层对里面的文件夹进行遍历
+ 为了方便项目中其他地方也用到计算文件大小,应该抽取成分类;不建议新增一个类,这样会增加内存,直接给NSString扩充一个分类;
+ 其他地方调用示例:
1. 方法一
```
NSString *size = @"/Users/chuanzhang/Desktop/daima".fileSize;
```

2. 方法二
```
long long size = @"/Users/chuanzhang/Desktop/daima".fileSize;
```

+ 下面方法一计算出大小,并转换成字符串,方法二计算出来的单位是字节(Byte)
+ **需要注意:iOS中1G == 1000Mb ==1000 * 1000kb == 1000 * 1000 * 1000b**
+ **计算文件大小属于耗时操作,如果文件比较大,那么在主线程用此方法会卡住主线程,造成用户体验很差;所以,调用此方法应该放在子线程**

------

## 1. 方法一

```
- (NSString *)fileSize
{
    // 总大小
    unsigned long long size = 0;
    NSString *sizeText = nil;
    // 文件管理者
    NSFileManager *mgr = [NSFileManager defaultManager];

    // 文件属性
    NSDictionary *attrs = [mgr attributesOfItemAtPath:self error:nil];
    // 如果这个文件或者文件夹不存在,或者路径不正确直接返回0;
    if (attrs == nil) return size;
    if ([attrs.fileType isEqualToString:NSFileTypeDirectory]) { // 如果是文件夹
        // 获得文件夹的大小  == 获得文件夹中所有文件的总大小
        NSDirectoryEnumerator *enumerator = [mgr enumeratorAtPath:self];
        for (NSString *subpath in enumerator) {
            // 全路径
            NSString *fullSubpath = [self stringByAppendingPathComponent:subpath];
            // 累加文件大小
            size += [mgr attributesOfItemAtPath:fullSubpath error:nil].fileSize;

           if (size >= pow(10, 9)) { // size >= 1GB
                sizeText = [NSString stringWithFormat:@"%.2fGB", size / pow(10, 9)];
            } else if (size >= pow(10, 6)) { // 1GB > size >= 1MB
                sizeText = [NSString stringWithFormat:@"%.2fMB", size / pow(10, 6)];
            } else if (size >= pow(10, 3)) { // 1MB > size >= 1KB
                sizeText = [NSString stringWithFormat:@"%.2fKB", size / pow(10, 3)];
            } else { // 1KB > size
                sizeText = [NSString stringWithFormat:@"%zdB", size];
            }

        }
        return sizeText;
    } else { // 如果是文件
        size = attrs.fileSize;
         if (size >= pow(10, 9)) { // size >= 1GB
                sizeText = [NSString stringWithFormat:@"%.2fGB", size / pow(10, 9)];
            } else if (size >= pow(10, 6)) { // 1GB > size >= 1MB
                sizeText = [NSString stringWithFormat:@"%.2fMB", size / pow(10, 6)];
            } else if (size >= pow(10, 3)) { // 1MB > size >= 1KB
                sizeText = [NSString stringWithFormat:@"%.2fKB", size / pow(10, 3)];
            } else { // 1KB > size
                sizeText = [NSString stringWithFormat:@"%zdB", size];
            }

        }
        return sizeText;
}

```

## 2. 方法二

```
- (unsigned long long)fileSize
{
    // 总大小
    unsigned long long size = 0;
    NSString *sizeText = nil;
    NSFileManager *manager = [NSFileManager defaultManager];

    BOOL isDir = NO;
    BOOL exist = [manager fileExistsAtPath:self isDirectory:&isDir];

    // 判断路径是否存在
    if (!exist) return size;
    if (isDir) { // 是文件夹
        NSDirectoryEnumerator *enumerator = [manager enumeratorAtPath:self];
        for (NSString *subPath in enumerator) {
            NSString *fullPath = [self stringByAppendingPathComponent:subPath];
            size += [manager attributesOfItemAtPath:fullPath error:nil].fileSize;

            }
        }else{ // 是文件
            size += [manager attributesOfItemAtPath:self error:nil].fileSize;
        }
        return size;
}

```

> ## 结语
> 本文转载自[简书](http://www.jianshu.com/);
> 作者-[船长](http://www.jianshu.com/u/6f76b136c31e)
> 感谢开源,感谢作者的贡献!

