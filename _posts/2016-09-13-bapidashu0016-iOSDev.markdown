---
layout:     post
title:      "AFNetworking3.1 在请求头中加入cookie"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-09-13 13:23:10
author:     "扒皮大叔"
header-img: "img/post-bg-0016.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 原创
---

> ## 前言
> 最近遇到一个新的项目的一个问题,后台要求在请求中带上cookie,增加安全性方面的验证,所以要求我们在登陆以后把固定的值放到cookie里面,然后放到请求头里面传给他们,这样就可以验证这个账号的登录状态和进行简单的加密,以避免不必要的登录等请求

## 1.首先我们要先获得响应体中后台给我们的cookie
```
NSDictionary *fields = ((NSHTTPURLResponse*)task.response).allHeaderFields;
NSLog(@"fields = %@",[fields description]);
NSURL *url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@",ET_BASE_URL,urlString]];
NSLog(@"\n==============url%@========================\n",url);
NSLog(@"%@---cookie",fields[@"Set-Cookie"]);
//获取cookie方法
NSString *cooking1;
NSString *cooking2;

NSArray *cookies = [NSHTTPCookie cookiesWithResponseHeaderFields:fields forURL:url];
for (NSHTTPCookie *cookie in cookies) {
    if ([cookie.name isEqualToString:@"XXXX"]) {
        cooking1 = [NSString stringWithFormat:@"%@=%@",cookie.name,cookie.value];
    }else if ([cookie.name isEqualToString:@"XXXX"]) {
        cooking2 = [NSString stringWithFormat:@"%@=%@",cookie.name,cookie.value];
    }
    NSLog(@"cookie,name:= %@,valuie = %@",cookie.name,cookie.value);
}
//拼接cooking
NSString *Cookie = [NSString stringWithFormat:@"%@;%@",cooking1,cooking2];
[[NSUserDefaults standardUserDefaults] setObject:Cookie forKey:ET_COOKIE];
//设置框架的cooking
[self loadSavedCookies]; 

```
1.1 打印后的结果为 

```
2017-08-14 16:52:59.664713+0800 iOSForEimTin[1077:457541] fields = {
    "Cache-Control" = "no-cache";
    "Content-Length" = 201;
    "Content-Type" = "application/json; charset=utf-8";
    Date = "Mon, 14 Aug 2017 08:52:59 GMT";
    Server = Apache;
    "Set-Cookie" = "XXXX=20c397d49d4d0b4020b060a81473fee8; Path=/; HttpOnly, _xsrf=Y29JQnhxR3RIR2k3ajMwdkdQckI4TUVJZXhpV3ZxMGw=|1502700779577082631|a197fa83cd4dec0058a1f801ec38c2ea2121b24e; Max-Age=31536000; Path=/, XXXX=49e25c245e7b6440d47e0c494782ecd6; Path=/; HttpOnly, XXXX=29!7f9dec25050a72110a2e2d665dee22b1; Max-Age=604800; Path=/; Secure; HttpOnly";
}
2017-08-14 16:52:59.664886+0800 iOSForEimTin[1077:457541] 
==============urlhttp://120.77.48.42:8781/api/auth/login========================
2017-08-14 16:52:59.664904+0800 iOSForEimTin[1077:457541] XXXX=20c397d49d4d0b4020b060a81473fee8; Path=/; HttpOnly, _xsrf=Y29JQnhxR3RIR2k3ajMwdkdQckI4TUVJZXhpV3ZxMGw=|1502700779577082631|a197fa83cd4dec0058a1f801ec38c2ea2121b24e; Max-Age=31536000; Path=/, XXXX=49e25c245e7b6440d47e0c494782ecd6; Path=/; HttpOnly, XXXX=29!7f9dec25050a72110a2e2d665dee22b1; Max-Age=604800; Path=/; Secure; HttpOnly---cookie
2017-08-14 16:52:59.666049+0800 iOSForEimTin[1077:457541] cookie,name:= XXXX,valuie = 20c397d49d4d0b4020b060a81473fee8
2017-08-14 16:52:59.666088+0800 iOSForEimTin[1077:457541] cookie,name:= _xsrf,valuie = Y29JQnhxR3RIR2k3ajMwdkdQckI4TUVJZXhpV3ZxMGw=|1502700779577082631|a197fa83cd4dec0058a1f801ec38c2ea2121b24e
2017-08-14 16:52:59.666108+0800 iOSForEimTin[1077:457541] cookie,name:= XXXX,valuie = 49e25c245e7b6440d47e0c494782ecd6
2017-08-14 16:52:59.666125+0800 iOSForEimTin[1077:457541] cookie,name:= XXXX,valuie = 29!7f9dec25050a72110a2e2d665dee22b1

```

1.2 上面可以看到我把整个响应头header都打印出来了,里面`Set-Cookie`对应的字符串就是后台给我传的cookie,我们只要按照后台的需要将对应的字段,对应的键值放到请求头的`Cookie`字段里面就好了,然后我们把需要的字段用偏好设置保存到本地,在需要用的时候,拿出来放到请求头里面就好了,(上面的XXXX是我们后台需要的字段)下面是设置请求头的代码


1.3 保存cookie,设置请求头

```
//合适的时机加载持久化后Cookie 一般都是app刚刚启动的时候
- (void)loadSavedCookies{
//    NSArray *cookies = [NSKeyedUnarchiver unarchiveObjectWithData: [[NSUserDefaults standardUserDefaults] objectForKey: @"org.skyfox.cookiesave"]];
//    NSHTTPCookieStorage *cookieStorage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
//    
//    for (NSHTTPCookie *cookie in cookies){
//        NSLog(@"cookie,name:= %@,valuie = %@",cookie.name,cookie.value);
//        [cookieStorage setCookie:cookie];
//    }
    
    
    NSString *str = [[NSUserDefaults standardUserDefaults] objectForKey:ET_COOKIE];
    [self.requestSerializer setValue:str forHTTPHeaderField:@"Cookie"];
    
    NSLog(@"这是header----%@",self.requestSerializer.HTTPRequestHeaders);
    NSString *str1 = [self.requestSerializer valueForHTTPHeaderField:@"Cookie"];
    NSLog(@"这是cookie---%@",str1);
    
}
```
1.4 上面的打印

```
2017-08-14 16:52:59.667978+0800 iOSForEimTin[1077:457541] 这是header----{
	Accept-Language = zh-Hans-CN;q=1;
	Cookie = XXXX=29!7f9dec25050a72110a2e2d665dee22b1;eimtin_sess=49e25c245e7b6440d47e0c494782ecd6;
	User-Agent = XXXX/1.0 (iPhone; iOS 10.3.3; Scale/2.00);
}

2017-08-14 16:52:59.668012+0800 iOSForEimTin[1077:457541] 这是cookie---XXXX=29!7f9dec25050a72110a2e2d665dee22b1;XXXX=49e25c245e7b6440d47e0c494782ecd6

```

1.5 OK 大功告成!





