---
layout:     post
title:      "Alamofire Post JSON String"
subtitle:   "未来已经来临, 只是尚未流行"
date:       2018-05-03 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Swift
---

## HTTP 协议

HTTP（HyperText Transfer Protocol），超文本传输协议，是一个基于TCP实现的应用层协议。

### 报文格式

HTTP1.0的报文有两种类型：请求和相应。其报文格式分别为：

#### 请求报文格式

请求方法 URL HTTP/版本号  
请求首部字段(可选)  
空行  
body(只对Post请求有效)

#### 响应报文格式

HTTP/版本号 返回码 返回码描述  
应答首部字段(可选)  
空行  
body

## HTTP 提交表单

通常, 我们使用 HTTP 的 POST方法 来提交表单, 也就是附带 Dictionary 格式的参数. 比如:  

	let urlString = "https://api.blockcypher.com/v1/bcy/test/txs/
	let parameters = ["inputs": [["addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"]]],
                          "outputs": [["addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"], "value": 1000000]]]
	let jsonString = JSON(parameters).rawString()
	Alamofire.request(urlString, method: .post, parameters: parameters).responseJSON { (response) in
            guard let result = response.result.value else { return }
            print(result)
        }

## HTTP Header

然而, POST 请求不仅限于提交表单, 还可以直接在 header, body 中附带额外信息. header 中附带的参数也是 Key-Value 格式, 一般的网络框架都有直接支持. 比如 AFNetworking:  

	NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
	configuration.HTTPAdditionalHeaders = @{@"key" : @"vlaue"};
	afManager = [[AFHTTPSessionManager alloc] initWithBaseURL:[NSURL URLWithString:fz_baseURL] sessionConfiguration:configuration];


Alamofire:  

	let headers: HTTPHeaders = [
    "key": "value"
	]
	Alamofire.request("https://httpbin.org/headers", headers: headers).responseJSON { response in
	    debugPrint(response)
	}


## HTTP Body

有时候我们需要直接在 body 中附带一个 JSON, 这就要求不得使用之前的提交表单的方法. Alamofire 的 JSON Encoding 这个类型为我们提供了这样的功能, 它会创造一个放置在 Request 的 body 中的代表参数的 JSON 对象. HTTP header 中的 Content-Type 被设置为 application/json.  

	let parameters: Parameters = [
    "foo": [1,2,3],
    "bar": [
        "baz": "qux"
    ]
	]
	// Both calls are equivalent
	Alamofire.request("https://httpbin.org/post", method: .post, parameters: parameters, encoding: JSONEncoding.default)
	// HTTP body: {"foo": [1, 2, 3], "bar": {"baz": "qux"}}