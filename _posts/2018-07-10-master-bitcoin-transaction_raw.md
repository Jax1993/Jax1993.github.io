---
layout:     post
title:      "精通比特币 -- 交易构建细节, 解锁脚本和锁定脚本"
date:       2018-07-10 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 区块链
---


## 数据结构

一个比特币交易, 包含一个或多个 UTXO 作为输入, 一个交易目的地址输出, 通常还包括一个找零的输出. 数据结构如下:  

	public struct Transaction {
		public let version: Int32
		public let txInCount: VarInt
		public let inputs: [TransactionInput]
		public let txOutCount: VarInt
		public let outputs: [TransactionOutput]
		public let lockTime: UInt32
    }
    
	public struct TransactionInput {
		public let previousOutput: TransactionOutPoint
		public let scriptLength: VarInt
		public let signatureScript: Data
		public let sequence: UInt32
	}
	
	public struct TransactionOutput {
		public let value: Int64
		public let scriptLength: VarInt
		public let lockingScript: Data
	}

## 构建过程

### 查询 UTXO 和 公钥私钥

查询可用 UTXO, 找到合适的 UTXO 集合用于构建交易输入. 因为需要生成每个 UTXO 的解锁脚本, 请确认程序可以获取到 UTXO 对应的公钥私钥.

### 交易输入的解锁脚本

TransactionInput 的 signatureScript 字段是**解锁脚本**, 需要计算得到. 但是我们会发现解锁脚本中的签名要通过交易本身才能获取, 所以还不知道, 所以用UTXO中的锁定脚本暂时代替. 这样就构建了一个临时的原始交易. 使用 UTXO 的是要对原始交易签名, 就得到了解锁脚本的签名部分. 再加上标志位和公钥部分, 就是解锁脚本.

省略了 UTXO 和 私钥查询, 解锁脚本的 Swift 实现如下:

	//标志位01000000
	var value_SIGHASH_ALL = UInt32(Signature.SIGHASH_ALL).littleEndian
	let data_signature_SIGHASH_ALL = Data(buffer: UnsafeBufferPointer(start: &value_SIGHASH_ALL, count: 1))
	//原始交易 hash
	let _txHash = Crypto.sha256sha256(_tx.serialized() + data_signature_SIGHASH_ALL)
	//使用 UTXO 对应的私钥对原始交易签名
	let signature: Data = try? Crypto.sign(_txHash, privateKey: privateKey)
	//标志位01
	var value_SIGHASH_ALL_8 = UInt8(Signature.SIGHASH_ALL).littleEndian
	let data_signature_SIGHASH_ALL_8 = Data(buffer: UnsafeBufferPointer(start: &value_SIGHASH_ALL_8, count: 1))
	//公钥
	let utxoPublicKey = hdprivateKey.publicKey()
	var utxoPublicKey_raw_count = UInt8(utxoPublicKey.raw.count)
	let data_utxoPublicKey_raw_count = Data(buffer: UnsafeBufferPointer(start: &utxoPublicKey_raw_count, count: 1))
	//解锁脚本
	let unlockingScript: Data = Data([UInt8(signature.count + 1)]) + signature + data_signature_SIGHASH_ALL_8 + data_utxoPublicKey_raw_count + utxoPublicKey.raw

### 交易输出的锁定脚本

TransactionOutput 的 lockingScript字段是**锁定脚本**, 需要计算得到.

	let toPubKeyHash = Base58.decode(paybackAddress).dropFirst().dropLast(4)
	let lockingScript = Script.buildPublicKeyHashOut(pubKeyHash: toPubKeyHash)



### 交易构建算法如下

1. 预估手续费
2. 根据预估手续费选择合适的 UTXO 集合
3. 使用 UTXO 构建交易输入, **使用 UTXO 的锁定脚本代替解锁脚本**
4. 构建交易输出
5. 构建原始交易(此时的交易输入中的解锁脚本并非真正的解锁脚本)
6. 生成原始交易的 hash
7. 重新构建交易输入, 循环之前选择的 UTXO 集合, 对每一个 UTXO 生成真正的解锁脚本
8. 重新构建真正的交易
9. 计算交易长度和所需要的手续费, 如果预估手续费低于实际手续费, 则循环到步骤1


