---
layout:     post
title:      "Objective-C 工程构建最佳实践"
subtitle:   "Objective-C 工程构建最佳实践"
date:       2017-02-23 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - iOS
---

希望看到这篇文章的初学者能少走一点弯路.   
后续会整理分享 MVP 写法, 解决 VIewController 代码庞大杂糅问题.  
欢迎讨论.


## 编码规范

最重要的写在最前面.  
羡慕 Java 程序猿, 因为他们永远不会为了大括号换不换行, 指针前后是否需要空格这样的问题打起来.  
但我们会.  
一定要仔细看完看完看完.  [禅与 Objective-C 编程艺术](https://yourtion.gitbooks.io/objc-zen-book-cn/).  
优雅的编码风格就应该精确到每一个空格.

## 新建工程

### 确定编程语言

该规范基于 Objective-C, 部分点不涉及具体语言, 可以参考. 

### 最小支持 iOS 版本

如果不是有特殊产品需求, 建议 iOS8, 甚至 iOS9, 具体 iOS 版本占比可以参考[官方统计](https://developer.apple.com/support/app-store/)和[非官方统计](https://david-smith.org/iosversionstats/).  
如果产品定位不涉及到必须对特殊人群的考虑, 真的没必要支持过低的 iOS 版本, 那样会让工程变得难以维护, 严重影响开发效率和程序健壮性.

### 类前缀
因为 Objective-C 没有包或者命名空间的概念, 所以需要加上特定的类前缀, 一般是工程缩写, 比如 WeChat 的前缀可以是 WC 😂. 类前缀可以在工程中统一设置, 这样每次创建新的类的时候就会自动加上前缀.

### 代码版权信息修改
就是 CopyRight 那些内容, 其实也就是指定 Organization 即可. 很多初学者不会去设置这个选项, 很多公司也不会要求, 但作为一个代码洁癖践行者. 顺便一提, 给自己取一个好看的英文名很重要, 比如 Laruence... 代码签名啊喂, 别经常换, 出了 bug 是要根据签名找你算账的.

### 是否支持旋转方向

一般不需要.

### 不使用 Xib 或 Storyboard
关于这一点比较尴尬, Apple 好像一直都比较推崇 Storyboard, 因为的确可以节省大量的时间, 避免大量的重复代码, 但是也的确存在诸如团队协作容易 git 冲突, Storyboard 庞大, 复用性不好, 半天找不到代码等问题. 更倾向于使用纯代码布局.

### 使用 git 进行版本控制
不要忘了添加 .gitignore, 可以在 [gitignore.io](gitignore.io) 自动生成, 哦, 一般会把 pod 也 ignore. 需要注意 commit 规范, 比如 commit信息应该包括 类型 fix | refactor | feature, 简要描述 , 可选详细描述和影响范围等. 需要注意分支管理规范, 比如应该 master 上不提供开发, 而始终指向稳定版本, 不同的 dev 分支可以对应正在开发的版本, 正在修改或完善的大的功能模块. 这里点到为止, 具体相关内容需要 Google.

## 目录结构

一个良好的工程目录是非常重要的, 它直接关系到整个项目的开发过程, 甚至约束我们的类图设计. 关于目录结构也有比较多的方案被讨论, 不过一般项目主要是两种方向, 一个是以工程架构 MVC/MVP 为主以功能模块划分为辅, 另一个是以功能模块为主以工程架构划分为辅. 其实第一种方案明显存在着诸多问题, 此处不展开. 更多的讨论就是围绕第二种方案提出的一些改进. 以下是我的目录结构划分

- DemoAPP
	- AppDelegate
		- DAAppDelegate
		- DAAppDelegate(Setup)
	- Module 
		- Home
			- Controller
				- DAHomeViewController 
			- Presenter
				- DAHomePresenter
			- View
			- Model
			- supporting  
				- DAHomeConstant.h 
		- Drawer
		- Account
		- Moments
		- MyPosts
	- Networking
	- DataCenter 
	- Utils
	- Categories
	- ThirdLib


## 工程架构

### MVP概述

待补充

## 依赖管理

### Cocoapods

Cocoapods 是 Objective-C 和 Swift 用来管理依赖的一个工具. 常见的库都可以一键集成, 这对于开发者来说简直是太方便了, 尤其是对于一些 SDK, 手动集成一般都需要做很多的配置, 如果使用 Cocoapods 来集成则只需要写一行配置就可以搞定. 不建议手动将三方库拖到工程里, 除非是自己不得不对某些库进行特殊修改.  
对于初学者来说, 最难的就是 Cocoapods 安装和安装了也用不了的问题了😂. 其实你需要的仅仅只是一个靠谱的翻墙工具, 然后也别去看什么国内的文章怎样怎样, 照着官网搞就是了. 关于翻墙请看文章下面相关部分.

### 使用私有 Cocoapods 管理自己的公共类库

待补充

## 资源文件管理

### 图标资源

- 存放于 Assets.xcassets 文件夹, 不要自定义 Images 文件夹
- 大小一般需要给定 @2X 和 @3X
- 注重资源文件命名规范, 推荐一种命名方式: 模块\_位置\_名称\_状态, 比如 home\_tabbar\_discover\_selected
- 按模块分别组织到对应的文件中, 便于管理

## 多语言支持和字符串管理

多语言支持和字符串管理, 即便你不确定产品之后会不会做国际化, 但是将字符串统一管理肯定是必要的. 建议字符串都写在一个文件里, 不必按模块写到不同的字符串文件, 这样做的好处是方便翻译和字符串重用. 但是要注意的是, 所有字符串都写在一个文件里, 很容易变得混乱, 毕竟动不动好几千个字符串是再正常不过的, 所以用注释或者其他手段将每一个模块的字符串分割开是必要的. 一些公共的字符串, 可以划分成一个单独的模块, 比如

	//Localizable.strings
	/**
	 * NSLocalizedString(<#key#>, nil)
 	 */

	/*************************** Public ****************************/
	"common_btn_ok_title" = "OK";
	"common_btn_cancel_title" = "Cancel";
	/***************************************************************/

	/*************************** Home *****************************/
	"home_vc_title" = "Home";
	/***************************************************************/

## 代码组织

虽然在编码规范和 MVP 概述里都有提到代码组织的问题, 但是在这里还是选了几个点列出来.因为这个真的很重要, 如果你维护过一个不拘一格的老程序员写下的不拘一格的代码, 我相信你会理解我的.

### ViewController 的代码组织

首先必须明确 Viewcontroller 的职责, 它主要用来管理 View, 接受用户交互以及数据的调用和绑定等.切记, 这里不是处理业务逻辑和其他计算逻辑的地方.如果你的ViewController 代码超过了四五百行, 那么可能你得重新思考一下你的设计了. 不排除特别特别复杂的界面.  
建议用编辑标记将 ViewController 进行划分, 一个常规的 ViewController 大致是这个样子

	#pragma mark - Life
	- (void)viewDidLoad 
	...
	#pragma mark - XXXPresenterDelegate
	- (void)gotoDetailVC 
	...
	#pragma mark - EventResponse
	- (void)didTappedConfirmBtn:(UIButton *)sender
	...
	#pragma mark - UITableViewDelegate

	#pragma mark - UITableViewDataSource

	#pragma mark - Helper
	
关于 PresenterDelegate 可以参看 MVP 介绍.  
关于 TableViewDataSource, 有人提出将数据源从 ViewController 中分离的思想, 以达到给 VIewController 瘦身的目的, 但其实从我个人的实践经验来看, 这样处理得不偿失, 事实上反而让代码更难以阅读和维护.如果对 Cell 进行了很好的封装, 数据源方法并不会太庞大.  
关于 Helper 只是用于处理一些半通用的逻辑, 比如有时候会协助处理 Cell 配置相关的内容.

### 自定义 View

在进行 UI 构建的时候, 我们经常会需要自定义一些 View. 通过自定义 View, 我们可以实现更通用的View 的重用, 比如弹出的警告框框和常见的cell等, 这样极大地减少了编码工作量, 推动了工程 UI 的统一性. 即便不是通用的自定义 View, 也可以使 ViewController 中的代码得到极大的简化, 使工程结构更加清晰.

**确定需要暴露哪些接口**

比如初始化方法, reloadWithObject等. 接口设计原则是尽可能简单, 只对外部暴露必要的信息.

**初始化子控件**

初始化方法有很多, 推荐的一种方式是, 将每一个控件都作为.m 文件的匿名分类中的属性, 并为每一	个控件写一个 buildXXX 方法, 并在 initWithFame 中调用这个方法
	
	@interface DADrawerTopView ()
	@property (nonatomic, strong) UILabel *nameLab;
	@end

	- (instancetype)initWithFrame:(CGRect)frame {
		if (self = [super initWithFrame:frame]) {
   			[self buildNameLab];
		}
  		return self;
	}
	
	#pragma mark - Builder
	- (void)buildNameLab {
		_nameLab = [[UILabel alloc] init];
		[self addSubview:_nameLab];
	}

**布局和约束**

布局一般可以使用 Fame 布局, 也可以使用 Masonry 布局.  
需要注意的是这两种布局方式不要在同一个 View 中混用. 而且如果是使用 Auto Layout 最好是在初始化的时候就添加相关的依赖. 如果你想要是用 Auto Layout, 请确保你已经查阅过 Apple 的文档 [Auto Layout Guide
](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html).

Frame 布局:

	- (void)layoutSubviews {
	    [super layoutSubviews];
	    _nameLab.frame = CGRectMake(20.f, 20.f, 100.f, 20.f);
	}

Masonry 布局:

	- (instancetype)initWithFrame:(CGRect)frame {
	    if (self = [super initWithFrame:frame]) {
	        [self buildNameLab];
	        [self setupConstraints];
	    }
	    return self;
	}
	
	- (void)setupConstraints {
	    [_nameLab mas_makeConstraints:^(MASConstraintMaker *make) {
	        make.left.equalTo(self).offset(20.f);
	        make.right.equalTo(self).offset(-20.f);
	        make.top.equalTo(self).offset(20.f);
	        make.height.mas_equalTo(20.f);
	    }];
	}

## 工具类

### 通用的工具类

诸如根据十六进制字符串取颜色, 字符串 MD5 加密, 图片圆角等, 可以放到对应 UIKit 类的Category 中, 建议自己定义的 Category 中的所有方法都加上前缀. 关于这些工具类, 虽然网上有很多, 但是建议不要一股脑儿全部拖进工程里.

	//UIColor+Util.h
	+ (UIColor *)da_colorWithHexString;
	
	//NSString+Util.h
	- (NSString *)da_md5String;

设备信息在开发中会频繁用到, 可以自定义一个 DADeviceInfo 的类, 用来提供获取需要的设备信息的方法.

	//DADeviceInfo.h
	/**
	 * 其实拿不到真正的 DeviceID, Apple 不让你这么做
	 * 一般是通过 identifierForVendor 存储在 KeyChain 中来当做设备的唯一 ID
	 */
	+ (NSString *)deviceID;
	+ (NSString *)deviceName;

### 工程相关的工具类

一般工程中会有一些确定的颜色, 比如通用普通字体颜色, 通用背景颜色等, 可以自定义 UIColor 的 Category 来管理.

	//UIColor+DAColor
	+ (UIColor *)da_whiteBgColor;
	+ (UIColor *)da_defaultTextColor;

字体一般也是固定的几个, 也可以通过分类来管理

	//UIFont+WJFont.h
	+ (UIFont *)da_textFont18;
	+ (UIFont *)da_textFont16;
	+ (UIFont *)da_textFont14;

## 关于翻墙

不能翻墙的日子, 我差不多就是一个废人了.  
作为一名 "有理想有追求" 的 CV 工程师, 翻墙是编码的第一步.  
推荐组合: [搬瓦工](https://bwh1.net/) + [ShadowSocks](https://shadowsocks.org/en/index.html).