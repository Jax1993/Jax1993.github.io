---
layout:     post
title:      "公共代码管理 -- CocoaPods 私有库的创建"
date:       2018-06-29 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - iOS 开发
---

## 相关概念

- Cocoapods 是 iOS 开发常用的依赖管理工具. 本文主要介绍如何使用 Cocoapods 创建私有仓库.

- Repo 是存放 podspec 的容器. 比如 Cocoapods 存放那些开源项目的 podspec 的容器是一个 Repo, 你自己托管在开源中国或者其他平台的 podspec 容器又是另一个 Repo. 可以使用命令 `pod repo list` 查看本地的 Repo. 

- Podspec 是一个存放在 Repo 中的描述文件, 用来描述开源或者私有项目的相关信息.
	
## 创建步骤

### 创建一个空的 Repo 作为 Specs 的容器.
 
就像创建普通托管项目一样, 在代码托管平台先创建一个空的仓库. 然后执行:

	$ pod repo add MyRepo https://coding.net/jax/MyRepo.git
	
### 创建项目

创建 Pod 的所需要的项目工程文件, 如下:

	 pod lib create podTestLibrary
	 
该命令会需要填写一些相关信息, 比如编程语言, 开发平台等. 然后会自动生成模板文件.
	 
### 修改 podspec 文件

打开工程, 修改 podspec 文件. 填写版本信息等.

### 添加项目代码

...

### 本地测试配置好的 podspec 文件是否可用

	pod lib lint --allow-warnings
	
### 向私有的 Spec Repo 中提交 podspec

	pod repo push MyRepo PodTestLibrary.podspec
	
### Podfile 中使用

添加 source :

	source 'https://gitee.com/jax/MyRepo.git
