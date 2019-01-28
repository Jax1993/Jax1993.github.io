---
layout:     post
title:      "Objective-C MVP 设计模式"
subtitle:   "Objective-C MVP 设计模式"
date:       2017-02-23 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - iOS
---


传统的 MVC 设计模式对于 iOS 端的开发来说, 存在着比较严重的问题. 最为突出的就是 ViewController 代码量杂糅庞大, 代码可复用性低, 维护难度大. 我在学习 Android 开发时, 参照 Google 官方的 MVP 设计模式, 提出了 Objective-C 版本的 MVP 设计模式.  


## MVP 概述

MVP: 即 M--Model, V--View, P--Presenter. Model 层主要封装了数据模型, 也可以包括一些模型的相关方法; View 层除了包括传统的视图 View 之外, 还包括 ViewController, 主要封装了视图以及视图上相关的手势响应和跳转等; Presenter 是主要的逻辑处理类, 包括网络请求, 数据库访问, 复杂的计算操作等.


代码组织依然以 ViewController 为基础, ViewController 以自定义的 View 来完成视图构建, 并强引用一个与 ViewController 一一对应的 Presenter. 该 Presenter 处理 ViewController 所需要的相关逻辑, 并通过定义代理协议使 ViewController 可以处理相关逻辑或者网络的回调.


![MVP](https://github.com/Jax1993/Something/blob/master/Image/post-mvp0.png?raw=true)

## MVP 举例

### 示例代码

	//ViewController.m 文件  
	//实现 Presenter 的代理协议, 强引用 presenter, 并初始化  
	@interface FZIndexViewController () <
	FZIndexPresenterDelegate,
	>
	
	@property (nonatomic, strong) FZIndexPresenter *presenter;
	
	@end
	
	@implementation FZIndexViewController
	
	- (instancetype)init {
    if (self = [super init]) {
        _presenter = [[FZIndexPresenter alloc] init];
        _presenter.delegate = self;
    }
    return self;
	}
	
	- (void)viewDidLoad {
    [super viewDidLoad];
    
    [self setupUI];  
    //调用 Presenter 的方法来进行网络加载  
    [self.presenter loadData];
    }
	
	@end
	
	//Presenter.h 文件
	//Presenter 的代理协议
	@protocol FZIndexPresenterDelegate <NSObject>

	- (void)didLoadData;

	@end

	@interface FZIndexPresenter : NSObject

	//由 presenter 持有数据, ViewController 需要渲染 UI 时访问该数据结构
	@property (nonatomic, strong) NSArray *data;
	//代理 指向对应的 ViewController
	@property (nonatomic, weak) id<FZIndexPresenterDelegate> delegate;
	//相关逻辑方法
	- (void)loadData;

	@end
	
### 拓展

大多数 Presenter 都需要执行加载数据, 上拉刷新等操作, 并且需要让ViewController 显示相关操作结果. 为了减少可重复代码, 我设计了 MVP 基础头文件, 定义了通用的基础协议.

	//FZMVPProtocol.h 文件
	/**
	 presenter 的代理基础协议, 由其他具体 presenter 的协议拓展
	 实际上是由 ViewController 实现
	 */  
	@protocol FZBasePresenterDelegate <NSObject>

	@optional
	- (void)showMsg:(NSString *)msg;

	@end


	/**
	 具备加载数据或者加载更多数据的 "presenter 的代理" 遵循的协议
	 实际上是由 ViewController 实现
	 */  
	@protocol FZLoadDataPresenterDelegate <FZBasePresenterDelegate>

	@optional
	- (void)reloadContent;
	- (void)didLoadMoreData;
	- (void)didLoadMoreWithNoData;

	@end


	/**
	 具备加载数据的 presenter 应该遵循的基础协议
	 实际上由 Presnter 实现
	 */  
	@protocol FZLoadDataPresenter <NSObject>

	@optional
	- (void)loadData;
	- (void)loadMoreData;

	@end
	
	
## 实践

在我的多个项目中, 已经广泛使用了该 MVP 设计模式, 可以极大地简化 ViewController 的代码, 实现了良好的逻辑界面分离.