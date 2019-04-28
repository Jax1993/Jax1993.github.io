---
layout:     post
title:      "精通比特币 -- 交易细节, 解锁脚本和锁定脚本"
date:       2018-07-10 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/space.jpg"
catalog: true
tags:
    - 区块链
---


## 数据结构

首先整体地了解一下比特币交易的数据结构. 一个比特币交易, 包含一个或多个 UTXO 作为输入, 一个交易目的地址的 UTXO 输出, 通常还包括一个找零 UTXO 输出. 总输入减去总输出的部分作为手续费奖励给矿工. 以下是相关的数据结构定义示例:  

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

**解锁脚本需要获取 UTXO 对应的私钥**, 而从公钥是不能够推导出私钥的. 所以, 我们需要在本地建立一个公钥私钥的映射, 或者说, 持有钱包所拥有的公钥私钥对.  

关于公钥私钥的保存, 提供一个简单的思路. 在钱包启动的时候, 在内存中加载足够多的私钥公钥对, 并且保存在内存中. 什么是足够多? 比特币标准是, 连续二十个地址没有交易的时候, 不再加载后续地址.

### 交易输入的解锁脚本

交易输入的 signatureScript 字段是解锁脚本(\<Sig>\<PubK>).  
解锁脚本是一个“解决”或满足被锁定脚本在一个输出上设定的花费条件的脚本，它将允许输出被消费.  
**构建交易的过程需要解锁脚本, 但是解锁脚本中的签名部分(Sig)需要通过交易本身才能获取, 而交易本身现在还正在构建, 所以暂时还不知道真正的解锁脚本. 所以用 UTXO 中的锁定脚本暂时代替交易输入的解锁脚本, 从而构建一个临时的原始交易.**   
构建原始交易之后, 在交易的二进制末尾加上签名类型, 一般是 0x01000000, 然后进行两次 sha256，就得到了原始交易的 hash. 使用 UTXO 的私钥对原始交易的 hash 签名, 末尾加上编码类型 0x01，开头加上一个字节的签名长度编码(注意, 编码长度是签名长度加 1, 因为末尾加了一个字节的编码类型). 这样就得到了解锁脚本的签名部分(Sig).  
解锁脚本的公钥部分(\<PubK>)比较简单, 获取 UTXO 对应的秘钥的公钥, 然后在公钥的二进制开头加上一个字节的公钥长度, 就得到了公钥部分.  
拼接 \<sig>\<PubK>, 就是解锁脚本.  

**需要特别注意的是: 如果交易有多个输入, 在计算解锁脚本构建原始交易的时候, 除了当前正在构建的输入, 其他输入的解锁脚本字段(signatureScript)需要设置为空**  

省略了 UTXO 和 私钥查询, 解锁脚本的 Swift 实现如下:

    ////循环构建每一个解锁脚本, 注意每一次的 transaction 都不一样哦

    //签名类型,一般是 0x01000000
    var value_SIGHASH_ALL = UInt32(Signature.SIGHASH_ALL).littleEndian
    let data_signature_SIGHASH_ALL = Data(buffer: UnsafeBufferPointer(start: &value_SIGHASH_ALL, count: 1))
    //原始交易 hash
    let _txHash = Crypto.sha256sha256(_tx.serialized() + data_signature_SIGHASH_ALL)
    //使用 UTXO 对应的私钥对原始交易签名
    let signature: Data = try? Crypto.sign(_txHash, privateKey: privateKey)
    //编码类型 0x01
    var value_SIGHASH_ALL_8 = UInt8(Signature.SIGHASH_ALL).littleEndian
    let data_signature_SIGHASH_ALL_8 = Data(buffer: UnsafeBufferPointer(start: &value_SIGHASH_ALL_8, count: 1))
    //公钥
    let utxoPublicKey = hdprivateKey.publicKey()
    var utxoPublicKey_raw_count = UInt8(utxoPublicKey.raw.count)
    let data_utxoPublicKey_raw_count = Data(buffer: UnsafeBufferPointer(start: &utxoPublicKey_raw_count, count: 1))
    //解锁脚本
    let unlockingScript: Data = Data([UInt8(signature.count + 1)]) + signature + data_signature_SIGHASH_ALL_8 + data_utxoPublicKey_raw_count + utxoPublicKey.raw

### 交易输出的锁定脚本

TransactionOutput 的 lockingScript字段是**锁定脚本**.  
锁定脚本是一个放置在输出上面的花费条件：它指定了今后花费这笔输出必须要满足的条件。锁定脚本往往含有一个公钥或比特币地址(公钥哈希值).
**特别注意的是，Pay2ScriptHash 和 Pay2PublicKeyHash 的算法是有区别的。**

    let toPubKeyHash = Base58.decode(toAddress).dropFirst().dropLast(4)
    let lockingScript = Script.buildPublicKeyHashOut(pubKeyHash: toPubKeyHash)
    
    //Script.swift
  
    // Standard Transaction to Bitcoin address (pay-to-pubkey-hash)  
    public static func buildPublicKeyHashOut(pubKeyHash: Data) -> Data {
       let tmp: Data = Data() + OpCode.OP_DUP + OpCode.OP_HASH160 +         UInt8(pubKeyHash.count) + pubKeyHash + OpCode.OP_EQUALVERIFY
       return tmp + OpCode.OP_CHECKSIG
    }
    
    // Pay2ScriptHash
    public static func buildScriptHashOut(pubKeyHash: Data) -> Data {
        var tmp = Data() + OP_HASH160 + OP_0 + pubKeyHash + OP_EQUAL
        return tmp
    }



### 交易构建算法如下

1. 预估手续费

2. 根据预估手续费选择合适的 UTXO 集合

3. 构建交易输出

4. 循环, 构建原始交易, 生成原始交易的 hash, 对每一个 UTXO 生成真正的解锁脚本

5. 重新构建真正的交易

6. 计算交易长度和所需要的手续费, 如果预估手续费低于实际手续费, 则循环到步骤1


