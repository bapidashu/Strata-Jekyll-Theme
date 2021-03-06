---
layout:     post
title:      "「原创」iOS多线程之GCD的用法"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-09-03 12:00:00
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
> 看下面代码即可,懒得打字    
   



```
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    NSLog(@"我开始点击了");
#pragma mark - 这是两种队列
    //1.创建串行队列(任务会一个一个的执行)
//    dispatch_queue_t chuanQueue = dispatch_queue_create("test1.queue", DISPATCH_QUEUE_SERIAL);
    //2.创建并发队列(任务会并发同时执行)
//    dispatch_queue_t bingQueue = dispatch_queue_create("test2.queue", DISPATCH_QUEUE_CONCURRENT);
#pragma mark - 这是任务的两种执行方法(同步和异步)
    //3.创建同步执行任务
//    dispatch_sync(chuanQueue, ^{
//        NSLog(@"%@---我是同步任务",[NSThread currentThread]);
//    });
    //4.创建异步执行任务
//    dispatch_async(chuanQueue, ^{
//        NSLog(@"%@---我是异步任务",[NSThread currentThread]);
//    });
    //一个一个的执行任务就是指串行执行任务
    //5.总共有三种队列(加上主队列),两种执行方法,所以应该有6种组合
    /**
     1.并发队列+同步执行(没有创建子线程,一个一个的执行任务)
     2.并发队列+异步执行(创建了多个新线程,同时执行任务)
     3.串行队列+同步执行(没有创建子线程,一个一个的执行任务)
     4.串行队列+异步执行(创建了一个新线程,一个一个的执行任务)
     5.主队列(主线程)+同步执行(没有创建子线程,一个一个的执行任务)
     6.主队列(主线程)+异步执行(没有创建子线程,一个一个的执行任务)
     */
    //5.1---并发队列+同步执行
    //[self syncConcurrent];
    //5.2---并发队列+异步执行
    //[self asyncConcurrent];
    //5.3---串行队列+同步执行
    //[self syncSerial];
    //5.4---并发队列+异步执行
    //[self asyncSerial];
    //5.5---主队列+同步执行
    //[self syncMain];
    //5.6---主队列+异步执行
    //[self asyncMain];
    //我们知道当在主线程中执行主队列的同步任务,会形成死锁,下面我们试下在其他线程中执行主队列的同步任务
    dispatch_queue_t queue = dispatch_queue_create("test6.queue", DISPATCH_QUEUE_CONCURRENT);
    //dispatch_queue_t queue = dispatch_queue_create("test6.queue", DISPATCH_QUEUE_SERIAL);
//    dispatch_async(queue, ^{
//        [self syncMain];
//    });
    //我们会发现我们用并发队列或者串行队列的异步执行的时候,上面的死锁一个一个的在主线程中执行完毕了
    //GCD之前的通信
//    dispatch_async(queue, ^{
//        //执行耗时操作
//        for (int i = 0; i < 70; i ++) {
//            NSLog(@"我是第一个同步任务");
//            NSLog(@"%@---我是异步务的线程",[NSThread currentThread]);
//        }
//        
//        for (int i = 0; i < 70; i ++) {
//            NSLog(@"我是第二个同步任务");
//            NSLog(@"%@---我是异步务的线程",[NSThread currentThread]);
//        }
//        
//        for (int i = 0; i < 70; i ++) {
//            NSLog(@"我是第三个同步任务");
//            NSLog(@"%@---我是异步务的线程",[NSThread currentThread]);
//        }
//        
//        dispatch_async(dispatch_get_main_queue(), ^{
//            NSLog(@"%@---我现在回到了主线程----",[NSThread currentThread]);
//        });
//    });
    
//    for (int i = 1; i < 50; i++) {
//        dispatch_async(queue, ^{
//            NSLog(@"%d___%@",i, [NSThread currentThread]);
//            
//            dispatch_async(dispatch_get_main_queue(), ^{
//                NSLog(@"%@---我现在回到了主线程----",[NSThread currentThread]);
//            });
//        });
//    }
    //6.GCD的栅栏
    //[self GCDZhaLanAAsync];
    //7.GCD的快速遍历
    //[self apply];
    //8.GCD的队列组,有时候我们需要这种操作,分别异步执行两个耗时操作当两个耗时操作执行完毕后就回到主线程刷新UI,这就用到了队列组
    dispatch_group_t group = dispatch_group_create();//创建全局并发队列
    dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        //第一个耗时操作
        NSLog(@"我是异步第一个操作---%@",[NSThread currentThread]);
    });
    dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        //第一个耗时操作
        NSLog(@"我是异步第二个操作---%@",[NSThread currentThread]);
    });
    dispatch_group_notify(group, dispatch_get_main_queue(), ^{
        //回到主线程
        NSLog(@"我回到了主线程");
    });
}
#pragma mark GCD的快速遍历
- (void)apply {  
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_apply(60, queue, ^(size_t index) {
        NSLog(@"%zd------%@",index, [NSThread currentThread]);
    });
}
#pragma mark GCD的栅栏操作
- (void)GCDZhaLanAAsync {
    dispatch_queue_t zhaLan = dispatch_queue_create("test7.queue", DISPATCH_QUEUE_CONCURRENT);
    //2.创建异步任务
    NSLog(@"并发队列任务初始化完毕--%@",[NSThread currentThread]);
//    dispatch_async(zhaLan, ^{
//        for (int i = 0; i < 2; i ++) {
//            NSLog(@"我是第一个同步任务");
//            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
//        }
//    });
    for (int i = 1; i < 10; i++) {
        dispatch_async(zhaLan, ^{
            NSLog(@"%d___%@",i, [NSThread currentThread]);
        });
    }
    dispatch_barrier_async(zhaLan, ^{
        NSLog(@"我是栅栏----%@",[NSThread currentThread]);
    });
//    dispatch_async(zhaLan, ^{
//        for (int i = 0; i < 2; i ++) {
//            NSLog(@"我是第二个同步任务");
//            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
//        }
//    });
    for (int i = 1; i < 10; i++) {
        dispatch_async(zhaLan, ^{
            NSLog(@"%d___%@",i, [NSThread currentThread]);
        });
    }
    dispatch_barrier_async(zhaLan, ^{
        NSLog(@"我是第二个栅栏----%@",[NSThread currentThread]);
    });
    for (int i = 1; i < 10; i++) {
        dispatch_async(zhaLan, ^{
            NSLog(@"%d___%@",i, [NSThread currentThread]);
        });
    }
//    dispatch_async(zhaLan, ^{
//        for (int i = 0; i < 2; i ++) {
//            NSLog(@"我是第三个同步任务");
//            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
//        }
//    });
    NSLog(@"主队列任务全部执行完");
}
#pragma mark 主队列+异步执行
- (void)asyncMain {
    //1.创建主队列
    dispatch_queue_t mainQueue = dispatch_get_main_queue();
    //2.创建异步任务
    NSLog(@"主队列任务初始化完毕--%@",[NSThread currentThread]);
    dispatch_async(mainQueue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第一个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    dispatch_async(mainQueue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第二个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    dispatch_async(mainQueue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第三个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    NSLog(@"主队列任务全部执行完");
}
#pragma mark 主队列+同步执行
- (void)syncMain {
    //1.创建主队列
    dispatch_queue_t mainQueue = dispatch_get_main_queue();
    //2.创建同步任务
    NSLog(@"主队列任务初始化完毕--%@",[NSThread currentThread]);
    dispatch_sync(mainQueue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第一个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    dispatch_sync(mainQueue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第二个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    dispatch_sync(mainQueue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第三个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    NSLog(@"主队列任务全部执行完");
    //我们会发现在控制台中只停留在了第一段log,也就是我们常说的死锁,因为我们把任务放在了我们的主队列中,但是- (void)syncMain 也是主队列中的犯法,所以程序先在主队列中执行执行到第一个同步任务的时候就把这个同步任务加到了主队列中,但是因为是同步执行所以都一个执行同步任务就在等待主队列把- (void)syncMain这个方法执行完毕,而- (void)syncMain这个方法又在等待这个同步任务执行完才能执行下面的代码或者执行完,所以他们两个都在互相等着对方执行完,所以就卡主了,这就叫死锁
}
#pragma mark 串行队列+异步执行
- (void)asyncSerial {
    //1.创建串行队列
    dispatch_queue_t queue = dispatch_queue_create("test5.queue", DISPATCH_QUEUE_SERIAL);
    NSLog(@"串行队列任务初始化完毕--%@",[NSThread currentThread]);
    //2.创建异步任务
    for (int i = 1; i < 10; i++) {
        dispatch_async(queue, ^{
            NSLog(@"%d___%@",i, [NSThread currentThread]);
        });
    }
    NSLog(@"串行队列任务全部执行完--%@",[NSThread currentThread]);
}
#pragma mark 串行队列+同步执行
- (void)syncSerial {
    //1.创建串行队列
    dispatch_queue_t queue = dispatch_queue_create("test5.queue", DISPATCH_QUEUE_SERIAL);
    NSLog(@"串行队列任务初始化完毕--%@",[NSThread currentThread]);
    //2.创建同步任务
    for (int i = 1; i < 10; i++) {
        dispatch_sync(queue, ^{
            NSLog(@"%d___%@",i, [NSThread currentThread]);
        });
    }
    NSLog(@"串行队列任务全部执行完--%@",[NSThread currentThread]);
}
#pragma mark 并发队列+异步执行
- (void)asyncConcurrent {
    //1.创建并发队列
    dispatch_queue_t queue = dispatch_queue_create("test4.queue", DISPATCH_QUEUE_CONCURRENT);
    NSLog(@"并发队列任务初始化完毕--%@",[NSThread currentThread]);
    //2.创建异步任务
//    dispatch_async(queue, ^{
//        for (int i = 0; i < 4; i ++) {
//            NSLog(@"我是第一个异步任务");
//            NSLog(@"%@---我是异步任务的线程",[NSThread currentThread]);
//        }
//    });
//    
//    dispatch_async(queue, ^{
//        for (int i = 0; i < 4; i ++) {
//            NSLog(@"我是第二个异步任务");
//            NSLog(@"%@---我是异步任务的线程",[NSThread currentThread]);
//        }
//    });
//    
//    dispatch_async(queue, ^{
//        for (int i = 0; i < 4; i ++) {
//            NSLog(@"我是第三个异步任务");
//            NSLog(@"%@---我是异步任务的线程",[NSThread currentThread]);
//        }
//    });
//    
//    dispatch_async(queue, ^{
//        for (int i = 0; i < 4; i ++) {
//            NSLog(@"我是第四个异步任务");
//            NSLog(@"%@---我是异步任务的线程",[NSThread currentThread]);
//        }
//    });
//    
//    dispatch_async(queue, ^{
//        for (int i = 0; i < 4; i ++) {
//            NSLog(@"我是第五个异步任务");
//            NSLog(@"%@---我是异步任务的线程",[NSThread currentThread]);
//        }
//    });
    for (int i = 1; i < 60; i++) {
        dispatch_async(queue, ^{
            NSLog(@"%d___%@",i, [NSThread currentThread]);
        });
    }
    NSLog(@"并发队列任务全部执行完--%@",[NSThread currentThread]);
}
#pragma mark 并发队列+同步执行
- (void)syncConcurrent {
    //1.创建并发队列
    dispatch_queue_t queue = dispatch_queue_create("test3.queue", DISPATCH_QUEUE_CONCURRENT);
    NSLog(@"并发队列任务初始化完毕");
    //2.创建同步任务
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第一个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第二个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    dispatch_sync(queue, ^{
        for (int i = 0; i < 2; i ++) {
            NSLog(@"我是第三个同步任务");
            NSLog(@"%@---我是同步任务的线程",[NSThread currentThread]);
        }
    });
    NSLog(@"并发队列任务全部执行完");
}
```


> 结束,通过上面的控制台打印,可以很清楚的知道GCD 的几种基本用法