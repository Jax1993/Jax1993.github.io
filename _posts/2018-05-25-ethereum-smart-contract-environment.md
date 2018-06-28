---
layout:     post
title:      "以太坊智能合约开发环境配置"
subtitle:   "以太坊智能合约开发环境配置"
date:       2018-05-25 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 区块链
---



## 以太坊

以太坊（英语：Ethereum）是一个开源的有智能合约功能的公共区块链平台。通过其专用加密货币以太币（Ether，又称“以太币”）提供去中心化的虚拟机（称为“以太虚拟机”Ethereum Virtual Machine）来处理点对点合约。以太坊的概念首次在2013至2014年间由程序员Vitalik Buterin，受比特币启发后提出，大意为“下一代加密货币与去中心化应用平台”，在2014年通过ICO众筹得以开始发展。

## 智能合约

以太坊最重要的技术贡献就是智能合约。智能合约是存储在区块链上的程序，可以协助和验证合约的谈判和运行。以太坊的智能合约可以数种用图灵完备的编程语言写成。因为合约内容公开，合约可以证明其宣称的功能是真实的，例如虚拟赌场可以证明它是公平的。另一方面，合约的公开性也表示如果合约中有漏洞，任何人都可以立刻看到，而修正程序可能会需要一些时间。

开发者可以在以太坊上方便地发布自己的 Token, 本质上也就是部署一个智能合约, 这个过程需要花费一定的 ETH. 

## 合约标准

在以太坊上发布 Token 需要遵循一定的标准. 最主要的两个标准是 ERC20 和 ERC721. OpenZeppelin 是一个方便开发者编写安全的智能合约的库. 开发者可以借助 OpenZeppelin 更加简便地实现合约标准, 并且更加安全.

### ERC20

大多数货币或者积分类型的 Token 都遵循 ERC20 标准.

	// ----------------------------------------------------------------------------
	// ERC Token Standard #20 Interface
	// https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md
	// ----------------------------------------------------------------------------
	contract ERC20Interface {
   		 function totalSupply() public constant returns (uint);
	   	 function balanceOf(address tokenOwner) public constant returns (uint balance);
		 function allowance(address tokenOwner, address spender) public constant returns (uint remaining);
   		 function transfer(address to, uint tokens) public returns (bool success);
    	 function approve(address spender, uint tokens) public returns (bool success);
	     function transferFrom(address from, address to, uint tokens) public returns (bool success);

    	 event Transfer(address indexed from, address indexed to, uint tokens);
	     event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
	}


### ERC721

ERC721 标准主要是定义了非同质的, 独一无二的 Token 标准. 比如加密猫的实现就是 ERC721 标准. 可以参考 [erc721.org](http://erc721.org/) 的解释. OpenZeppelin 中可以直接复用相关代码.

## 开发环境

Truffle 集成开发环境 + Ganache 私链GUI + zeppelin 合约标准库 + IntelliJ IDEA, Solidity 插件 + Metamask 本地钱包

### Truffle

Truffle 是一个以太坊智能合约开发框架，利用它可以方便地生成项目模板、编译合约、部署合约到区块链、测试合约等等. Truffle 是一个 Node.js 模块，因此要先安装 Node.js.  

	npm install -g truffle
	truffle init


### Ganache

Ganache 可以方便搭建以太坊开发的私有链. 因为合约开发过程中, 需要部署合约测试, 并且需要测试账号, 如果直接部署到以太坊显然不合理, 所以 Ganache 是很好的选择.

### OpenZeppelin

OpenZeppelin 是一个方便开发者编写安全的智能合约的库.

	npm install -E openzeppelin-solidity

### IntelliJ IDEA

IntelliJ IDEA 一般用来作为 JAVA 开发的编译器, 装上 Solidity 插件, 就可以方便地编写 Solidity 代码.

### MetaMask

MetaMask 是一款在谷歌浏览器Chrome上使用的插件类型的以太坊钱包.



