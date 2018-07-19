---
layout:     post
title:      "精通比特币 -- 椭圆曲线密码学"
date:       2018-07-19 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 区块链
    - 算法
---

**椭圆曲线密码学**（Elliptic Curve Cryptography）是比特币算法中很重要的一个算法. 学习椭圆曲线密码学对于普通的开发者来说不是必须的, 因为已经有太多的 SDK 帮我们屏蔽了这些细节. 但是要真正进入区块链的世界, 深入理解其底层的密码学算法是必须的.


## 概念

**椭圆曲线**（英语：Elliptic curve，缩写为EC）为一代数曲线，被下列式子所定义:

y<sup>2</sup>=x<sup>3</sup>+ax+b  

其是无奇点的；亦即，其图形没有尖点或自相交。

若 y<sup>2</sup>=P(x), 其中P为任一没有重根的三次或四次多项式，然后可得到一亏格1的无奇点平面曲线，其通常亦被称为椭圆曲线。更一般化地，一亏格1的代数曲线，如两个三维二次曲面相交，即称为椭圆曲线。

![](http://upload-images.jianshu.io/upload_images/1785959-38f55b8ef0bdae9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**椭圆曲线的点倍积**(point multiplication)，指的是椭圆曲线上一个点沿着这条曲线不断的与自身相加，最终落在曲线另一个点上的(计算)过程。 



