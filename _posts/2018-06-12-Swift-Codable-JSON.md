---
layout:     post
title:      "JSON 解析--Swift4 的 Codable 协议"
date:       2018-06-12 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/solar-system.jpg"
catalog: true
tags:
    - Swift
---

开发中经常需要与网络交互, 或者存储数据到磁盘, 这些操作通常都要求对传输的数据进行编码和解码. JSON 是其中最常用的数据编码方式.   

虽然已经有很多第三方类库实现了 JSON 解析, 但是使用体验真的比较糟糕. 这与 Swift 的可选值有关. 明明很简单的数据模型转换却变得很棘手, 通常都需要比较繁杂的处理. 
幸运的是, Apple 终于在 Swift 4 的 Foundation 的模块中添加了对 JSON 解析的原生支持.    
这篇文章就主要介绍一下如何使用 Swift 4 的 Codable 协议实现 JSON - Model 转换.

Codable 协议是 Encodable 和 Decodable 的 type alias. 声明如下:   
	
	typealias Codable = Decodable & Encodable
	
## 最简单的编码和解码

如果你的类型的属性都已经遵循了 Codable, 那么该类型可以自动实现编码和解码. 已经实现了 Codable 协议的类型包括标准库类型的 **Double, Int, String** 等和 Foundation 类型 **Date, Data, URL** 等.

	struct User: Codable {
   		let id: Int
   		let name: String
	}
	
	func testJSONToModel() -> Void {
        let json: [String: Any] = ["id": 111, "name": "Jax"]
        let decoder = JSONDecoder()
        do {
            let data = try JSONSerialization.data(withJSONObject: json, options: .prettyPrinted)
            let user = try decoder.decode(User.self, from: data)
            print("name: \(user.name)")
        } catch {
            print(error)
        }
    }
    
    func testModelToJSON() -> Void {
        let user = User(id: 111, name: "Jax")
        let encoder = JSONEncoder()
        do {
            let data = try encoder.encode(user)
            guard let jsonString = String(data: data, encoding: String.Encoding.utf8) else { return }
            print(jsonString)
        } catch {
            print(error)
        }
    }	
	//输出:
	//name: Jax
	//{"id":111,"name":"Jax"}

## JSON 和 Model 键值数不一致

如果 Model 中定义的属性在 JSON 中没有对应的键, 或者该键对应的值是 null, 则 JSON to Model 过程会报错:

	keyNotFound(CodingKeys(stringValue: "age", intValue: nil)...
	
解决方法是将 Model 中不一定能在 JSON 找到对应键值的 属性设置成可选:

	struct User: Codable {
		let id: Int
		let name: String
   		let age: Int?
	}

**注意:**  
如果 JSON 是数组格式, 某些元素为空. 下面的例子是一个用户有一个书本数组, 但是数组中的某些书可能为空, 则 Model 相应的处理方式是:

	struct User: Codable {
		let id: Int
		let name: String
   		let age: Int?
   		let books: [Book?]
	}

## 嵌套

嵌套是很常见的. Codable 协议提供了良好的支持.

	struct Book: Codable {
   		let id: Int
   		let name: String
	}

	struct User: Codable {
   		let id: Int
   		let name: String
   		let age: Int?
   		let faivate: Book
   		let books: [Book]
	}

## 自定义键名

当 JSON 中的键名和属性名不一致时, 可以通过实现 CodingKeys 来自定义键名.

	struct User: Codable {
   		let id: Int
	 	let name: String
   		let age: Int?
    
   		enum CodingKeys: String, CodingKey {
      		case name = "username"
			case id
			case age
		}
	}
	
	//test
	func testJSONToModel() -> Void {
        let json: [String: Any] = ["id": 111, "username": "Jax"]
        let decoder = JSONDecoder()
        do {
            let data = try JSONSerialization.data(withJSONObject: json, options: .prettyPrinted)
            let user = try decoder.decode(User.self, from: data)
            print("name: \(user.name)")
        } catch {
            print(error)
        }
    }

## JSON 要求属性定义必须是可选, 但我不要

JSON 要求属性定义必须是可选, 即 JSON 中没有返回模型中定义的全部键值. 这样的话, 我们不得不在队形的模型中设置相关的属性为可选. 但我不并不想要这个属性成为可选属性, 因为在我的程序中会根据语义为该字段赋值. 不必要的可选属性会给程序设计带来不必要的麻烦.

	struct BookFromApi: Codable {
   		let id: Int
   		let name: String
  		let price: Double?
	}

	struct Book: Codable {
   		let id: Int
   		let name: String
   		let price: Double
	}
	
	//from API
	let bookFromAPI = BookFromApi(id: 1, name: "C++编程思想", price: nil)
	//转换, 为部分属性提供默认值
	let book = Book(id: bookFromAPI.id, name: bookFromAPI.name, price: 12.11)
	
上述做法, 定义了一个专门用来做 JSON 转换的类型, 然后用该类型的对象去初始化本来的需要转化的类型对象. 虽然很奇葩, 但是目前, 这个方法很好地解决了我遇到的问题.

