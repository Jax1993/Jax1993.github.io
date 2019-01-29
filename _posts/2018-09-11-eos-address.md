---
layout:     post
title:      "EOS 的私钥和地址"
date:       2018-09-11 15:31:00
author:     "你看那朵乌云儿"
header-img: "img/solar-system.jpg"
catalog: true
tags:
    - 区块链
---
      
### 私钥
     
私钥的本质是一个随机数, 这在之前的文章中已经提到过. 比特币私钥空间的大小是 2^256，这是一个非常大的数字。用十进制表示的话，大约是 10^77，而可见宇宙被估计只含有 10^80 个原子。

> 通常, 为了便于查看和导入导出, 私钥以 Base58 校验和编码格式显示，这种私钥格式被称为钱包导入格式（WIF，Wallet Import Format）。


EOS 私钥编码格式和 Bitcoin 的非压缩格式私钥编码完全一致.

>比如 "5JJEz7NcRGX8BnNjN7jpR9sry7HmxgRqZ1rtEQ45VHNMjgaKFv5"

具体算法如下: 

	//币种 wif 编码前缀, BTC, EOS 都是 0x80
	let wifHeaderHexStr = token.wif.fz_IntToHexString()
	let data_wifHeader = Data(hex: wifHeaderHexStr)
	//私钥的二进制格式
	let row = key
	//对前缀和私钥进行两次 sha256运算, 取首四字节校验和
	let check = Crypto.sha256sha256(data_wifHeader + row).prefix(4)
	//对 wif 前缀, 私钥以及校验和进行 Base58 编码, 即得到私钥的 wif 格式
	let wif = Base58.encode(data_wifHeader + row + check)


### EOS 地址

EOS地址的编码比较奇葩.

> 比如 "EOS7QMaFsE7H1jvrHVz6prDetrnqxupe3optbcrnXyfhS5feotpHU"

它的算法是：

	//公钥的二进制格式
	let pubRaw = hdPrivateKey.publicKey().raw
	//对公钥进行 RipeMD160 哈希
	let ripemd160 = Crypto.ripemd160(pubRaw)
	//取首四字节校验和
	let checksum = ripemd160.prefix(4)
	//将公钥和校验和进行 Base58 编码
	let base58 = Base58.encode(pubRaw + checksum)
	//加上 "EOS" 作为前缀
	let address = "EOS"+base58
	
	
可以在这个网站生成秘钥对测试.  
[eostea](https://eostea.github.io/eos-generate-key/)



