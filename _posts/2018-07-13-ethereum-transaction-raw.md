---
layout:     post
title:      "深入理解以太坊 -- 交易细节, RLP编码和签名"
date:       2018-07-13 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/space.jpg"
catalog: true
tags:
    - 区块链
---


## 以太坊交易数据结构

以太坊交易数据结构定义如下:  

	enum EthChainIDL: Int {
    	case COIN_ETH = 1
    	case COIN_ROPSTEN = 3
    	case COIN_RINKEBY = 4
	}

	struct SignTransaction {
    	let value: BigInt
    	let to: Address?
    	let nonce: BigInt
    	let data: Data
    	let gasPrice: BigInt
    	let gasLimit: BigInt
    
		//networkID
    	let chainID: Int
	}
	
对于 ETH, to 是接收者地址, value 是 ETH 数量, 单位是 Wei, gasLimit 一般是 2100, data 为空.  
对于 ERC20, to 是合约地址, value 是 ERC20 数量, 单位是相应的最小单位, gasLimit 可以是 50000 或者更高, data 中的数据是 transfer 函数的编码.

## RLP

[RLP(递归长度前缀)](https://github.com/ethereum/wiki/wiki/%5B%E4%B8%AD%E6%96%87%5D-RLP) 提供了一种适用于任意二进制数据数组的编码，RLP已经成为以太坊中对对象进行序列化的主要编码方式。 RLP的唯一目标就是解决结构体的编码问题；对原子数据类型（比如，字符串，整数型，浮点型）的编码则交给更高层的协议；以太坊中要求数字必须是一个大端字节序的、没有零占位的存储的格式（也就是说，一个整数0和一个空数组是等同的）。

RLP 编码定义如下：

- 对于 [0x00, 0x7f] 范围内的单个字节, RLP 编码内容就是字节内容本身。
- 否则，如果是一个 0-55 字节长的字符串，则RLP编码有一个特别的数值 0x80 加上字符串长度，再加上字符串二进制内容。这样，第一个字节的表达范围为 [0x80, 0xb7].
- 如果字符串长度超过 55 个字节，RLP 编码由定值 0xb7 加上字符串长度所占用的字节数，加上字符串长度的编码，加上字符串二进制内容组成。比如，一个长度为 1024 的字符串，将被编码为\xb9\x04\x00 后面再加上字符串内容。第一字节的表达范围是[0xb8, 0xbf]。
- 如果列表的内容（它的所有项的组合长度）是0-55个字节长，它的RLP编码由0xC0加上所有的项的RLP编码串联起来的长度得到的单个字节，后跟所有的项的RLP编码的串联组成。 第一字节的范围因此是[0xc0, 0xf7]
- 如果列表的内容超过55字节，它的RLP编码由0xC0加上所有的项的RLP编码串联起来的长度的长度得到的单个字节，后跟所有的项的RLP编码串联起来的长度的长度，再后跟所有的项的RLP编码的串联组成。 第一字节的范围因此是[0xf8, 0xff] 。


## ECDSA

**椭圆曲线数字签名加密算法**(Elliptic Curve Digital Signature Algorithm,ECDSA).  

有关 ECDSA 的几个理论概念的关系是这样的：椭圆曲线数字加密算法 ECDSA 是**数字签名算法**(DSA)的变例之一，ECDSA 的基础是 **椭圆曲线密码学**(Elliptic-curve cryptography，ECC)，而 ECC 的理论前提是**椭圆曲线上点的倍积**(Elliptic curve point multiplication)。

## 步骤

- 根据是 ETH 还是 ERC20, 构建相应的交易对象. 如果是 ERC20, data 字段是 transfer 函数的编码. 
- 对交易对象 RLP 编码并 keccak256 运算得到交易的 hash
- 用私钥对 hash 进行 ecdsa 签名得到签名(signature)
- 从签名(signature)中解析出 R, S, V
- 再次 RLP 编码交易信息 和 R, S, V, 得到交易的 data



