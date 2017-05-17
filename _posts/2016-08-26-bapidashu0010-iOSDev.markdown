---
layout:     post
title:      "「简书」iOS 加载webView进度条"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-08-26 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0006.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 简书
---

> ## 简介   
> 为了项目的敏捷性和简单性,现在的app常常会嵌入不少的H5页面,而如何优雅的显示这些web页面呢,我这里参照微信显示web页面的方式,做了一个导航栏下的加载进度条.因为项目最低支持iOS7,所以不能使用WKWebView来加载网页,只能使用 `UIWebView`,查看 `UIWebView`的API可以发现,并没有代理或是通知告诉我们`webView`加载了多少,所以这个进度条我决定用虚拟的方式来做,就是假装知道加载了多少.

**加载WebView的控制器WYWebController**

```
@interface WYWebController : UIViewController

@property (nonatomic, copy) NSString *url;

@end


#import "WLWebController.h"
#import "WLWebProgressLayer.h"
#import "UIView+Frame.h"

@interface WLWebController ()<UIWebViewDelegate>

@end

@implementation WLWebController
{
    UIWebView *_webView;

    WLWebProgressLayer *_progressLayer; ///< 网页加载进度条
}

- (void)viewDidLoad {
    [super viewDidLoad];

    self.view.backgroundColor = [UIColor whiteColor];
    [self setupUI];
}

- (void)setupUI {
    _webView = [[UIWebView alloc] initWithFrame:self.view.bounds];
    _webView.delegate = self;
    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:self.url]];
    [_webView loadRequest:request];

    _webView.backgroundColor = [UIColor whiteColor];

    _progressLayer = [WLWebProgressLayer new];
    _progressLayer.frame = CGRectMake(0, 42, SCREEN_WIDTH, 2);

    [self.navigationController.navigationBar.layer addSublayer:_progressLayer];
    [self.view addSubview:_webView];
}

#pragma mark - UIWebViewDelegate
/// 网页开始加载
- (void)webViewDidStartLoad:(UIWebView *)webView {
    [_progressLayer startLoad];
}

/// 网页完成加载
- (void)webViewDidFinishLoad:(UIWebView *)webView {
    [_progressLayer finishedLoad];

// 获取h5的标题
    self.title = [webView stringByEvaluatingJavaScriptFromString:@"document.title"];

}

/// 网页加载失败
- (void)webView:(UIWebView *)webView didFailLoadWithError:(NSError *)error {
    [_progressLayer finishedLoad];
}

- (void)dealloc {

    [_progressLayer closeTimer];
    [_progressLayer removeFromSuperlayer];
    _progressLayer = nil;
    NSLog(@"i am dealloc");
}

@end
```

**进度条`WYWebProgressLayer.h`**

```
#define SCREEN_WIDTH [UIScreen mainScreen].bounds.size.width

@interface WYWebProgressLayer : CAShapeLayer

- (void)finishedLoad;
- (void)startLoad;

- (void)closeTimer;

@end
```

`WYWebProgressLayer.m`这个类主要继承自`CAShapeLayer`,这里我主要想要使用`CAShaperLayer`的`strokeStart`[默认0]和`strokeEnd`[默认1]这两个属性来对进度条进行渲染,使用定时器更改`strokeEnd`的值使其看起来就像在一点一点的加载一样.   

```
#import "WYWebProgressLayer.h"
#import "NSTimer+addition.h"

static NSTimeInterval const kFastTimeInterval = 0.003;

@implementation WYWebProgressLayer {
    CAShapeLayer *_layer;

    NSTimer *_timer;
    CGFloat _plusWidth; ///< 增加点
}

- (instancetype)init {
    if (self = [super init]) {
        [self initialize];
    }
    return self;
}

- (void)initialize {
    self.lineWidth = 2;
    self.strokeColor = [UIColor greenColor].CGColor;

    _timer = [NSTimer scheduledTimerWithTimeInterval:kFastTimeInterval target:self selector:@selector(pathChanged:) userInfo:nil repeats:YES];
    [_timer pause];

    UIBezierPath *path = [UIBezierPath bezierPath];
    [path moveToPoint:CGPointMake(0, 2)];
    [path addLineToPoint:CGPointMake(SCREEN_WIDTH, 2)];

    self.path = path.CGPath;
    self.strokeEnd = 0;
    _plusWidth = 0.01;
}

- (void)pathChanged:(NSTimer *)timer {
    self.strokeEnd += _plusWidth;

    if (self.strokeEnd > 0.8) {
        _plusWidth = 0.002;
    }
}

- (void)startLoad {
    [_timer resumeWithTimeInterval:kFastTimeInterval];
}

- (void)finishedLoad {
    [self closeTimer];

    self.strokeEnd = 1.0;
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.25 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        self.hidden = YES;
        [self removeFromSuperlayer];
    });
}

- (void)dealloc {
//    NSLog(@"progressView dealloc");
    [self closeTimer];
}

#pragma mark - private
- (void)closeTimer {
    [_timer invalidate];
    _timer = nil; 
}
```

**DEMO**[https://github.com/wangyansnow/WYWebViewDemo](https://github.com/wangyansnow/WYWebViewDemo)

## 如何放到项目中   
只需要下载demo,然后把demo中的整个web文件夹拖入到项目中,使用方式例:

```
WYWebController *webVC = [WYWebController new];
webVC.url = @"https://www.baidu.com";
[self.navigationController pushViewController:webVC animated:YES];
```

> ## 结语
> 本文转载自简书作者[倚楼听风雨wing](http://www.jianshu.com/u/d201f33f3096),[iOS 加载webView进度条](http://www.jianshu.com/p/b32b9fb6cb0a).








