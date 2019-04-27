---
layout:     post
title:      "四种常见的 POST 提交数据方式"
subtitle:   "未来已经来临, 只是尚未流行"
date:       2018-05-03 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/the-sea.jpg"
catalog: true
tags:
    - Swift
---

HTTP/1.1 协议规定的 HTTP 请求方法有 OPTIONS、GET、HEAD、POST、PUT、DELETE、TRACE、CONNECT 这几种。其中 POST 一般用来向服务端提交数据，本文主要讨论 POST 提交数据的几种方式。

我们知道，HTTP 协议是以 ASCII 码传输，建立在 TCP/IP 协议之上的应用层规范。规范把 HTTP 请求分为三个部分：状态行、请求头、消息主体。类似于下面这样：

	<method> <request-URL> <version>
	<headers>

	<entity-body>
	
协议规定 POST 提交的数据必须放在消息主体（entity-body）中，但协议并没有规定数据必须使用什么编码方式。实际上，开发者完全可以自己决定消息主体的格式，只要最后发送的 HTTP 请求满足上面的格式就可以。  

以下介绍几个常用的编码方式。


## application/x-www-form-urlencoded

这应该是最常见的 POST 提交数据的方式了。浏览器的原生 <form> 表单，如果不设置 enctype 属性，那么最终就会以 application/x-www-form-urlencoded 方式提交数据。在服务端，比如 spring， 也是默认以这种方式处理请求。请求类似于下面这样

	POST http://www.example.com HTTP/1.1
	Content-Type: application/x-www-form-urlencoded;charset=utf-8

	title=test&sub%5B%5D=1&sub%5B%5D=2&sub%5B%5D=3

首先，Content-Type 被指定为 application/x-www-form-urlencoded；其次，提交的数据按照 key1=val1&key2=val2 的方式进行编码，key 和 val 都进行了 URL 转码。大部分服务端语言都对这种方式有很好的支持.

**Alamofire** 举例:

	let urlString = "https://api.blockcypher.com/v1/bcy/test/txs/
	let parameters = ["key1": "value1",
						 "key2": "value2"]
	let jsonString = JSON(parameters).rawString()
	Alamofire.request(urlString, method: .post, parameters: parameters).responseJSON { (response) in
            guard let result = response.result.value else { return }
            print(result)
        }

## multipart/form-data

这又是一个常见的 POST 数据提交的方式。我们使用表单上传文件时，必须让 <form> 表单的 enctype 等于 multipart/form-data. 这种方式一般用来上传文件，各大服务端语言对它也有着良好的支持。


## application/json

application/json 这个 Content-Type 作为响应头大家肯定不陌生。实际上，现在越来越多的人把它作为请求头，用来告诉服务端消息主体是序列化后的 JSON 字符串。 这种方案，可以方便的提交复杂的结构化数据，特别适合 RESTful 的接口。

**Alamofire** 举例:

	let parameters: Parameters = [
    "foo": [1,2,3],
    "bar": [
        "baz": "qux"
    ]
	]
	// Both calls are equivalent
	Alamofire.request("https://httpbin.org/post", method: .post, parameters: parameters, encoding: JSONEncoding.default)
	// HTTP body: {"foo": [1, 2, 3], "bar": {"baz": "qux"}}
	
## text/xml

这个用得少, 忽略.

## 附: 在 HTTP Header 中添加参数

可以直接在 HTTP header 中添加参数。常用来处理身份验证。

**AFNetworking**:  

	NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
	configuration.HTTPAdditionalHeaders = @{@"key" : @"vlaue"};
	afManager = [[AFHTTPSessionManager alloc] initWithBaseURL:[NSURL URLWithString:fz_baseURL] sessionConfiguration:configuration];


**Alamofire**:  

	let headers: HTTPHeaders = [
    "key": "value"
	]
	Alamofire.request("https://httpbin.org/headers", headers: headers).responseJSON { response in
	    debugPrint(response)
	}

