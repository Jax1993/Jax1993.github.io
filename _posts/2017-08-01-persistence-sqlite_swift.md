---
layout:     post
title:      "数据持久化方案 -- Sqlite for Swift 设计与实现"
date:       2017-08-01 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Swift
---

[SQLite.swift](https://github.com/stephencelis/SQLite.swift) 是一个 Swift 版本的类型安全的 SQlite 开源框架. 

## 简单使用

#### 创建数据库

	let db = try Connection("path/to/db.sqlite3")

#### 创建数据表

	let users = Table("users")
	let id = Expression<Int64>("id")
	let name = Expression<String?>("name")
	let email = Expression<String>("email")

	try db.run(users.create { t in
		t.column(id, primaryKey: true)
		t.column(name)
		t.column(email, unique: true)
	})
	
#### Insert

	let insert = users.insert(name <- "Alice", email <- "alice@mac.com")
	let rowid = try db.run(insert)
	
#### Find

	for user in try db.prepare(users) {
    print("id: \(user[id]), name: \(user[name]), email: \(user[email])")
    // id: 1, name: Optional("Alice"), email: alice@mac.com
	}

#### Update

	let alice = users.filter(id == rowid)
	try db.run(alice.update(email <- email.replace("mac.com", with: "me.com")))
	
#### Delete

	let alice = users.filter(id == rowid)
	try db.run(alice.delete())
	
#### count

	try db.scalar(users.count) 
	
## 线程安全

每个连接 (Connection) 都有自己的串行队列来执行语句, 并且可以跨线程安全地访问. 打开事务和保存点的线程将在事务打开时阻止其他线程执行语句.  

**注意, 这里的线程安全, 并不是说我们就可以想当然的把同一个数据库的操作随意放在多个线程当中. 这在业务逻辑上, 往往并不会符合我们的预期.**

如果需要在同一个数据库上维持多个连接, 那么可以考虑设置一个超时的 Handler, 但是这个操作并不能保证我们的数据库操作真正成功, 这种强时序顺序的设计不是一个好的多线程设计:

	db.busyTimeout = 5

	db.busyHandler({ tries in
		if tries >= 3 {
			return false
		}
		return true
	})


> Note: 默认超时是 0, 如果看到 `database is locked`, 请尝试同步访问数据库

#### 疑惑

我在开发的时候遇到一个问题, 目前没有找到解决方案.  

使用主线程异步没问题

	DispatchQueue.main.async {
		//insert some data
	}

使用 GlobalQueue 的时候, 就会 crash, 并且提示 `database is locked`, 即便是设置了	`db.busyTimeout = 5` 也依然 crash

	DispatchQueue.global.async {
		//insert some data
	}


## 代码设计和组织

#### 使用单例维持数据库的连接

	static let shared = UserDB()
   	private init() {
        do {
            db = try Connection(defaultDBPath)
            createTable()
        } catch {
            print("db Connection error: \(error)")
        }
    }

#### Swift 直接插入对象

iOS 开发中, 数据库操作通常都需要手写映射关系, 这给开发者带来了不必要的麻烦. Swift4 之后, 我们有了 Codable 协议, 这个特性使得我们可以直接向数据库插入一个对象.

	func insert(info: InfoEntry) -> Void {
        let table = Table(tableName)
        if exist(info: info) {
            update(info: info)
            return
        }
        
        do {
            let insert = try table.insert(info)
            try db.run(insert)
        } catch let error {
            FZlog.debug("insert error: \(error)")
        }
    }

#### 数据库升级

随着版本迭代, 不可避免的, 我们需要修改表的结构, 增加或删除字段. 而不同版本如果具有不同的数据库结构, 并且没有任何处理, 那么通常都会 crash, 因为找不到新增字段或者丢失了某些字段.

