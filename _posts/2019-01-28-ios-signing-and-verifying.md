---
layout:     post
title:      "iOS Security Framework 的签名和验证"
date:       2019-01-28 15:31:00
author:     "你看那朵乌云儿"
header-img: "img/space.jpg"
catalog: true
tags:
    - iOS
---
  
  
### Overview

为了在数据块上创建加密签名，首先要创建数据的散列，也就是对数据进行 hash 算法，这样就得到了数据的**摘要**，然后使用**私钥**加密此摘要，这样就得到了数据的**签名**。接收方使用您的公钥解密签名，同时独立地重新创建原始数据的散列（哈希）。如果解密后的哈希与计算出的哈希匹配，则接收方可以确保数据来自与公钥对应的私钥的所有者。  

通常，将数据块签名作为执行其他操作的附属工作。例如，作为发布应用程序的最后一步，Xcode 使用你的一个密码身份代表你对代码进行签名。

### 私钥

首先，签名算法需要一个私钥，这个私钥是**非对称加密**密码对的私钥部分。私钥的格式一般有 DER 和 PEM。下面的代码是从 PEM 格式的文件得到一个私钥：  
		
	static func getPrivateKeyFromFile() throws -> Data {
		let priPath = Bundle.main.path(forResource: "pri", ofType: ".key")!
		let priUrl = URL(fileURLWithPath: priPath)
		guard var priString = try? String(contentsOf: priUrl) else {
			throw PaymentError.privateKeyLoadFailed
		}
		let beginRange = priString.range(of: "-----BEGIN RSA PRIVATE KEY-----")!
		priString.removeSubrange(beginRange)		let endRange = priString.range(of: "-----END RSA PRIVATE KEY-----")!
			priString.removeSubrange(endRange)
			priString.removeAll { (c) -> Bool in
			c == "\n"
		}
		let decodedData = Data(base64Encoded: priString)!
		return decodedData
	}

### 签名

使用 Security Framework 对数据进去签名

	// 指定签名算法
	let algorithm = SecKeyAlgorithm.rsaSignatureMessagePKCS1v15SHA256
	
	// 错误处理
	var error: Unmanaged<CFError>?
	
	// 签名算法选项
	let options: [String: Any] = [kSecAttrKeyType as String: kSecAttrKeyTypeRSA,
                                      kSecAttrKeyClass as String: kSecAttrKeyClassPrivate,
                                      kSecAttrKeySizeInBits as String: 2048]
	
	// 私钥
	let cfKey = getPrivateKeyFromFile() as CFData
        
	guard let secKey = SecKeyCreateWithData(cfKey, options as CFDictionary, &error) else {
			print(error as Any)
			throw PaymentError.privateKeyLoadFailed
	}
	guard let signature = SecKeyCreateSignature(secKey, algorithm, requestData as CFData, &error) as Data? else {
		throw XXXXError
	}

### 签名验证

成功生成签名后，将数据和签名发送给任何相关方。然后使用您的公钥，收件人通过执行一组类似于签名过程的操作来验证签名。

	var error: Unmanaged<CFError>?
	guard SecKeyVerifySignature(publicKey, algorithm, data as CFData, signature as CFData, &error) else {
		throw error!.takeRetainedValue() as Error
	}
	
### 其他

我发现 Swift 中使用 Core Foundation 的 API 已经不再需要手动执行 release 啦。  
哈哈哈。。。 真是开心啊


















