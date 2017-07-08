---
layout:     post
title:      "「简书」iOS正则表达式的使用"
subtitle:   "iOS开发常见问题及解决方案"
date:       2016-09-10 12:00:00
author:     "扒皮大叔"
header-img: "img/post-bg-0009.jpg"
header-mask: 0.4
catalog:    true
tags:
    - iOS
    - 软件开发
    - 简书
    - 转载
---

> ## 今天要说的是正则表达式的使用


# 1. 先放一张图压压惊

![img](/img/post-bg-0008.jpg)


# 2. 什么是正则表达式    

```
正则表达式，又称正规表示法，是对字符串操作的一种逻辑公式。正则表达式可以检测给定的字符串是否符合我们定义的逻辑，也可以从字符串中获取我们想要的特定部分。它可以迅速地用极简单的方式达到字符串的复杂控制。
```

# 3. 语法介绍

```
@"^[0-9]+$"
　　它代表了字符串中只能包含>=1个0-9的数字，语法是不是有一些怪异？
　　下面我们先撇开iOS中的正则表达式的语法，用通俗的正则表达式语法来为介绍一下。（iOS语法与通俗的正则表达式语法相同，不同在于对转义字符的处理上(语言类的都相同)）
　　语法：
　　首先，特殊符号'^'和'$'。他们的作用是分别指出一个字符串的开始和结束。eg：
　　“^one”：表示所有以”one”开始的字符串（”one cat”，”one123″，·····）；
　　类似于:- (BOOL)hasPrefix:(NSString )aString;
　　“a dog$”：表示所以以”a dog”结尾的字符串（”it is a dog”，·····）；
　　类似于:- (BOOL)hasSuffix:(NSString )aString;
　　“^apple$”：表示开始和结尾都是”apple”的字符串，这个是唯一的~；
　　“banana”：表示任何包含”banana”的字符串。
　　类似于 iOS8的新方法- (BOOL)containsString:(NSString )aString,搜索子串用的。
　　‘'，'+'和'?'这三个符号，表示一个或N个字符重复出现的次数。它们分别表示“没有或更多”（[0,+∞]取整），“一次或更多”（[1,+∞]取整），“没有或一次”（[0,1]取整）。下面是几个例子：
　　“ab”：表示一个字符串有一个a后面跟着零个或若干个b（”a”, “ab”, “abbb”,……）；
　　“ab+”：表示一个字符串有一个a后面跟着至少一个b或者更多（ ”ab”, “abbb”,……）；
　　“ab?”：表示一个字符串有一个a后面跟着零个或者一个b（ ”a”, “ab”）；
　　“a?b+$”：表示在字符串的末尾有零个或一个a跟着一个或几个b（ ”b”, “ab”,”bb”,”abb”,……）。
　　可以用大括号括起来（{}），表示一个重复的具体范围。例如
　　“ab{4}”：表示一个字符串有一个a跟着4个b（”abbbb”）；
　　“ab{1,}”：表示一个字符串有一个a跟着至少1个b（”ab”,”abb”,”abbb”,……)；
　　“ab{3,4}”：表示一个字符串有一个a跟着3到4个b（”abbb”,”abbbb”)。
　　那么，“”可以用{0，}表示，“+”可以用{1，}表示，“?”可以用{0，1}表示
　　注意：可以没有下限，但是不能没有上限！例如“ab{,5}”是错误的写法
　　“ | ”表示“或”操作：
　　“a|b”：表示一个字符串里有”a”或者”b”；
　　“(a|bcd)ef”：表示”aef”或”bcdef”；
　　“(a|b)*c”：表示一串”a”"b”混合的字符串后面跟一个”c”；
　　方括号”[ ]“表示在括号内的众多字符中，选择1-N个括号内的符合语法的字符作为结果，例如
　　“[ab]“：表示一个字符串有一个”a”或”b”（相当于”a|b”）；
　　“[a-d]“：表示一个字符串包含小写的'a'到'd'中的一个（相当于”a|b|c|d”或者”[abcd]“）；
　　“^[a-zA-Z]“：表示一个以字母开头的字符串；
　　“[0-9]a”：表示a前有一位的数字；
　　“[a-zA-Z0-9]$”：表示一个字符串以一个字母或数字结束。
　　“.”匹配除“\r\n”之外的任何单个字符：
　　“a.[a-z]“：表示一个字符串有一个”a”后面跟着一个任意字符和一个小写字母；
　　“^.{5}$”：表示任意1个长度为5的字符串；
　　“\num” 其中num是一个正整数。表示”\num”之前的字符出现相同的个数，例如
　　“(.)\1″：表示两个连续的相同字符。
　　“10{1,2}” : 表示数字1后面跟着1或者2个0 (“10″,”100″)。
　　” 0{3,} ” 表示数字为至少3个连续的0 （“000”，“0000”，······）。
　　在方括号里用'^'表示不希望出现的字符，'^'应在方括号里的第一位。
　　“@[^a-zA-Z]4@”表示两个”@”中不应该出现字母）。
　　常用的还有：
　　“ \d ”匹配一个数字字符。等价于[0-9]。
　　“ \D”匹配一个非数字字符。等价于[^0-9]。
　　“ \w ”匹配包括下划线的任何单词字符。等价于“[A-Za-z0-9_]”。
　　“ \W ”匹配任何非单词字符。等价于“[^A-Za-z0-9_]”。
　　iOS中书写正则表达式，碰到转义字符，多加一个“\”,例如：
　　全数字字符：@”^\d+$”
```

# 4. 基本文档,语法和说明

```
//值的意思

.      匹配除换行符外的任意字符

\w   匹配字母或者数字的字符

\W   匹配任意不是字母或数字的字符

\s    匹配任意的空白符(空格、制表符、换行符)

\S    匹配任意不是空白符的字符

\d    匹配任意数字

\D    匹配任意非数字的字符

\b    匹配单词的结尾或者开头的字符

\B    匹配任意不是单词结尾或开头的字符

[^x]  匹配任意非x的字符。如[^[a-z]]匹配非小写字母的任意字符

^      匹配字符串的开头

$      匹配字符串的结尾


--------------------------------------------
//修饰的意思

*     匹配重复任意次数

+    匹配重复一次以上的次数

?      匹配一次或零次

{n}    匹配重复n次

{n,}   匹配重复n次或n次以上

{n,m} 匹配重复最少n次最多m次

```

# 5. 日常使用的分类

+ .h文件

```
//
//  NSString+RegularExpressions.h
//  
//
//  Created by 扒皮大叔 on 2017/7/4.
//
//  正则表达式

#import <Foundation/Foundation.h>

@interface NSString (RegularExpressions)
//匹配正则表达式
- (BOOL)isValidateByRegex:(NSString *)regex;
/**
 是否能够匹配正则表达式
 
 @param regex  正则表达式
 @param options     普配方式.
 @return YES：如果可以匹配正则表达式; 否则,NO.
 */
- (BOOL)matchesRegex:(NSString *)regex options:(NSRegularExpressionOptions)options;

- (BOOL)isValidPhoneNumber;//普通手机号匹配
- (BOOL)wl_isMobileNumberClassification;//Plus 版手机号匹配

- (BOOL)VerificationCode;

- (BOOL)isEmailAddress;//邮箱匹配

- (BOOL)isValidUrl;//网页地址匹配

- (BOOL)simpleVerifyIdentityCardNum;//身份证有效性
+ (BOOL)accurateVerifyIDCardNumber:(NSString *)value;//精确的身份证有效性判断

- (BOOL)validateCarType;//车型

- (BOOL)effectivePassword;//判断是否有特殊符号

- (BOOL)isIPAddress;//判断IP地址是否有效

- (BOOL)isMacAddress;//判断mac地址是否有效

- (BOOL)isValidChinese;//判断是否全是中文

- (BOOL)isValidPostalcode;//判断邮政编码是否正确

- (BOOL)isValidTaxNo;//判断是否是工商税号

/**
 @brief     是否符合最小长度、最长长度，是否包含中文,首字母是否可以为数字
 @param     minLenth 账号最小长度
 @param     maxLenth 账号最长长度
 @param     containChinese 是否包含中文
 @param     firstCannotBeDigtal 首字母不能为数字
 @return    正则验证成功返回YES, 否则返回NO
 */
- (BOOL)isValidWithMinLenth:(NSInteger)minLenth
                   maxLenth:(NSInteger)maxLenth
             containChinese:(BOOL)containChinese
        firstCannotBeDigtal:(BOOL)firstCannotBeDigtal;


/**
 @brief     是否符合最小长度、最长长度，是否包含中文,数字，字母，其他字符，首字母是否可以为数字
 @param     minLenth 账号最小长度
 @param     maxLenth 账号最长长度
 @param     containChinese 是否包含中文
 @param     containDigtal   包含数字
 @param     containLetter   包含字母
 @param     containOtherCharacter   其他字符
 @param     firstCannotBeDigtal 首字母不能为数字
 @return    正则验证成功返回YES, 否则返回NO
 */
- (BOOL)isValidWithMinLenth:(NSInteger)minLenth
                   maxLenth:(NSInteger)maxLenth
             containChinese:(BOOL)containChinese
              containDigtal:(BOOL)containDigtal
              containLetter:(BOOL)containLetter
      containOtherCharacter:(NSString *)containOtherCharacter
        firstCannotBeDigtal:(BOOL)firstCannotBeDigtal;
@end
```


------





> ## 本文内容来来源于简书文章,对其归纳整理,并写了一个分类,感谢开源,侵删
> 来源1:[iOS 正则表达式使用](http://www.jianshu.com/p/f19ba9246cbe),作者[Lonely](http://www.jianshu.com/u/8df293c6d308)
> 来源2:[iOS开发-正则表达式](http://www.jianshu.com/p/00da4d87b777),作者[sindri的小巢](http://www.jianshu.com/u/0cf7d455eb9e)

