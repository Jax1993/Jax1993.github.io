---
layout:     post
title:      "对象序列化 -- Protocol Buffers"
date:       2018-06-23 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Swift
---

# Protocol buffers 

[Protocol buffers](https://developers.google.com/protocol-buffers/) 是 Google 开发的一套语言无关, 平台无关, 可拓展的对象序列化机制. 类似于 XML, JSON, 但是更快, 更小, 更简单. 定义了数据结构之后, 便可以用多种语言的特定代码去读和写这些结构化的数据.  
不同于 JSON 和 XML等, Protocol buffers 的数据结构需要提前定义在 .proto 文件中, 使用过程中不同端需要协调定义的一致性.  
目前有2和3两个独立版本的语法, 就像 Python. 真是讨厌啊. 倾向于使用3, 就像 Python. 手动二哈吐舌头.

# Swift Protobuf

[Swift Protobuf](https://github.com/apple/swift-protobuf) 是 Apple 用 Swift 实现的 Google Protocol buffers.  

# Quick Start

### Install

using CocoaPods

	pod 'SwiftProtobuf', '~> 1.0'
	

### 编写 .proto

编写 .proto 文件描述数据结构.  

	//BookInfo.proto
	syntax = "proto3";

	message BookInfo {
	int64 id = 1;
	string title = 2;
	string author = 3;
	}

### 生成 .bp.swift

在终端, CD 到 .proto 所在文件夹, 执行

	protoc --swift_out=. *.proto
	
如果没有报错的话, 会生成与之对应的 Swift 文件, 这些文件定义了 Struct 结构和相关操作. 将这些文件添加到工程中.

### Usage

序列化和反序列化示例:

	let info0 = BookInfo.with {
		$0.id = 1735
		$0.title = "Even More Interesting"
		$0.author = "Jane Q. Smith"
	}

	// Serialize to binary protobuf format:
	let binaryData: Data = try info.serializedData()

	// Deserialize a received Data object from `binaryData`
	let decodedInfo = try BookInfo(serializedData: binaryData)

	// Serialize to JSON format as a Data object
	let jsonData: Data = try info.jsonUTF8Data()

	// Deserialize from JSON format from `jsonData`
	let receivedFromJSON = try BookInfo(jsonUTF8Data: jsonData)