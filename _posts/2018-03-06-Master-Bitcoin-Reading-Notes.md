---
layout:     post
title:      "精通比特币学习笔记"
subtitle:   "精通比特币学习笔记"
date:       2018-03-06 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/solar-system.jpg"
catalog: true
tags:
    - 区块链
---

未来已经来临, 只是尚未流行.  

## 比特币

比特币（英语：Bitcoin ）, 是一种去中心化，非普遍全球可支付的电子加密货币。比特币由中本聪（又译中本哲史（化名）于2009年1月3日，基于无国界的对等网络，用共识主动性开源软件发明创立。


## 区块链

区块链（英语：blockchain 或 block chain）是用分布式数据库识别、传播和记载信息的智能化对等网络, 也称为价值互联网。中本聪在2008年，于《比特币白皮书》中提出“区块链”概念，并在2009年创立了比特币社会网络，开发出第一个区块，即“创世区块”。


## 比特币核心

比特币核心 （英语：Bitcoin Core）是比特币以及其它加密货币参考的开源客户端。最初由中本聪以“Bitcoin”命名，后改为“Bitcoin Core”。该软件会验证整个区块链曾经做过的所有交易，并默认可用于转接资金。

## 秘钥和地址

比特币的所有权是通过数字密钥、比特币地址和数字签名来确定的。数字密钥实际上并不存储在网络中，而是由用户生成之后，存储在一个叫做钱包的文件或简单的数据库中。存储在用户钱包中的数字密钥完全独立于比特币协议，可由用户的钱包软件生成并管理，而无需参照区块链或访问网络。

一个比特币钱包中包含一系列的密钥对，每个密钥对包括一个私钥和一个公钥。私钥（k）是一个数字，通常是随机选出的。有了私钥，我们就可以使用椭圆曲线乘法这个单向加密函数产生一个公钥（K）。有了公钥（K），我们就可以使 用一个单向加密哈希函数生成比特币地址（A）。

通过椭圆曲线乘法可以从私钥计算得到公钥，这是不可逆转的过程：K = k * G 。其中k是私钥，G是被称为生成点的常数点，而K是所得公钥。其反向运算，被称为“寻找离散对数”——已知公钥K来求出私钥k——是非常困难的，就像去试验所有可能的k值，即暴力搜索。

比特币地址是一个由数字和字母组成的字符串，可以与任何想给你比特币的人分享。在交易中，比特币地址通常以收款方出现。比特币地址可由公钥经过单向的加密哈希算法得到。  
以公钥 K 为输入，计算其SHA256哈希值，并以此结果计算RIPEMD160 哈希值，得到一个长度为160位（20字节）的数字：  
A = RIPEMD160(SHA256(K))  
公式中，K是公钥，A是生成的比特币地址。  
通常用户见到的比特币地址是经过“Base58Check”编码的。

私钥必须保密。BIP0038加密方案是：输入一个比特币私钥，通常使用WIF编码过，base58chek字符串的前缀“5”。此外BIP0038加密方案需要一个长密码作为口令，通常由多个单词或一段复杂的数字字母字符串组成。


## 钱包

一个常见误解是，比特币钱包里含有比特币。 事实上，钱包里只含有钥匙。 “钱币”被记录在比特币网络的区块链中。 用户通过钱包中的密钥签名交易，从而来控制网络上的钱币。  
第一种类型是非确定性钱包（nondeterministic wallet），其中每个密钥都是从随机数独立生成的。密钥彼此无关。这种钱包也被称为“Just a Bunch Of Keys（一堆密钥）”，简称JBOK钱包。  
第二种类型是确定性钱包（deterministic wallet），其中所有的密钥都是从一个主密钥派生出来，这个主密钥即为种子（seed）。该类型钱包中所有密钥都相互关联，如果有原始种子，则可以再次生成全部密钥。确定性钱包中使用了许多不同的密钥推导方法。最常用的推导方法是使用树状结构，称为分级确定性钱包或HD钱包。  
确定性钱包由种子衍生创造。为了便于使用，种子被编码为英文单词，也称为助记词。

### 非确定性（随机）钱包

在最早的一批比特币客户端中（ Bitcoin Core，现在称作比特币核心客户端），钱包只是随机生成的私钥集合。这种类型的钱包被称作零型非确定钱包。举个例子，比特币核心客户端预先生成100个随机私钥，从最开始就生成足够多的私钥并且每个密钥只使用一次。这种钱包现在正在被确定性钱包替换，因为它们难以管理、 备份以及导入。随机密钥的缺点就是如果你生成很多私钥，你必须保存它们所有的副本。这就意味着这个钱包必须被经常性 地备份。每一个密钥都必须备份，否则一旦钱包不可访问时，钱包所控制的资金就付之东流。

### 确定性（种子）钱包

确定性，或者“种子”钱包包含通过使用单项离散函数而可从公共的种子生成的私钥。种子是随机生成的数字。这个数字也含有比如索引号码或者可生成私钥的“链码”。在确定性钱包中，种子足够恢复所有的已经产生的私钥，所以只用在初始创建时的一个简单备份就足以搞定。并且种子也足够让钱包导入或者导出。这就很容易允许使用者的私钥在钱包之间轻松转移。

### 分层确定性钱包（HD Wallets (BIP-32/BIP-44)）

确定性钱包被开发成更容易从单个“种子”中生成许多密钥。确定性钱包的最高级形式是通过BIP0032标准定义的HD钱包。HD钱包包含以树状结构衍生的密钥，使得父密钥可以衍生一系列子密钥，每个子密钥又可以衍生出一系列孙密钥，以此类推，无限衍生。

### 种子和助记词（BIP-39）

HD钱包具有管理多个密钥和地址的强大机制。由一系列英文单词生成种子是个标准化的方法，这样易于在钱包中转移、导出和导入，如果HD钱包与这种方法相结合，将会更加有用。 这些英文单词被称为助记词，标准由BIP-39定义。 今天，大多数比特币钱包（以及其他加密货币的钱包）使用此标准，并可以使用可互操作的助记词导入和导出种子进行备份和恢复。

### 钱包最佳实践

由于比特币钱包技术已经成熟，出现了一些常见的行业标准，使得比特币钱包具备广泛互操作，易于使用，安全和灵活的特性。这些常用的标准是：

助记码，基于BIP-39

HD钱包，基于BIP-32

多用途HD钱包结构，基于BIP-43

多币种和多帐户钱包，基于BIP-44

这些标准可能会随着发展而改变或过时，但是现在它们形成了一套互锁技术，这些技术已成为比特币的事实上的钱包标准。

这些标准已被广泛的软件和硬件比特币钱包采用，使所有这些钱包互操作。用户可以导出在其中一个钱包上生成的助记符，并将其导入另一个钱包，实现恢复所有交易，密钥和地址。

列举支持这些标准的软件钱包，包括（按字母顺序排列）Breadwallet，Copay，Multibit HD和Mycelium。列举支持这些标准的硬件钱包，包括（按字母顺序排列）Keepkey，Ledger和Trezor。

## 交易

比特币交易是比特币系统中最重要的部分。根据比特币系统的设计原理，系统中任何其他的部分都是为了确保比特币交易可以被生成、能在比特币网络中得以传播和通过验证，并最终添加入全球比特币交易总账簿（比特币区块链）。比特币交易的本质是数据结构，这些数据结构中含有比特币交易参与者价值转移的相关信息。比特币区块链是一本全球复式记账总账簿，每个比特币交易都是在比特币区块链上的一个公开记录。

### UTXO

比特币交易中的基础构建单元是交易输出。 交易输出是比特币不可分割的基本组合，记录在区块上，并被整个网络识别为有效。 比特币完整节点跟踪所有可找到的和可使用的输出，称为 “未花费的交易输出”（unspent transaction outputs），即UTXO。 所有UTXO的集合被称为UTXO集，目前有数百万个UTXO。 当新的UTXO被创建，UTXO集就会变大，当UTXO被消耗时，UTXO集会随着缩小。每一个交易都代表UTXO集的变化（状态转换）。

当我们说用户的钱包已经“收到”比特币时，我们的意思是，钱包已经检测到了可用的UTXO。通过钱包所控制的密钥，我们可以把这些UTXO花出去。 因此，用户的比特币“余额”是指用户钱包中可用的UTXO总和，而他们可能分散在数百个交易和区块中。 “一个用户的比特币余额”，这个概念是比特币钱包应用创建的派生之物。比特币钱包通过扫描区块链并聚集所有属于该用户的UTXO来计算该用户的余额 。大多数钱包维护一个数据库或使用数据库服务来存储所有UTXO的快速参考集，这些UTXO由用户所有的密钥来控制花费行为。

### 矿工费

大多数交易包含交易费（矿工费），这是为了确保网络安全而给比特币矿工的一种补偿。费用本身也作为一个安全机制，使经济上不利于攻击者通过交易来淹没网络。交易费是基于交易的千字节规模来计算的，而不是比特币交易的价值。  
交易的数据结构没有交易费的字段。  
交易费即输入总和减输出总和的余量：交易费 = 求和（所有输入） - 求和（所有输出）  

### 脚本

比特币交易脚本语言，称为脚本，是一种类似Forth的逆波兰表达式的基于堆栈的执行语言。  

#### 图灵非完备性

比特币脚本语言包含许多操作码，但都故意限定为一种重要的模式——除了有条件的流控制以外，没有循环或复杂流控制能力。这样就保证了脚本语言的图灵非完备性，这意味着脚本有限的复杂性和可预见的执行次数。脚本并不是一种通用语言，这些限制确保该语言不被用于创造无限循环或其它类型的逻辑炸弹，这样的炸弹可以植入在一笔交易中，引起针对比特币网络的“拒绝服务”攻击。记住，每一笔交易都会被网络中的全节点验证，受限制的语言能防止交易验证机制被作为一个漏洞而加以利用。

### 去中心化

比特币交易脚本语言是没有中心化主体的，没有任何中心主体能凌驾于脚本之上，也没有中心主体会在脚本被执行后对其进行保存。所以执行脚本所需信息都已包含在脚本中。

#### 锁定和解锁

比特币的交易验证引擎依赖于两类脚本来验证比特币交易：锁定脚本和解锁脚本。

锁定脚本是一个放置在输出上面的花费条件：它指定了今后花费这笔输出必须要满足的条件。 由于锁定脚本往往含有一个公钥或比特币地址（公钥哈希值），在历史上它曾被称为脚本公钥（scriptPubKey）  

解锁脚本是一个“解决”或满足被锁定脚本在一个输出上设定的花费条件的脚本，它将允许输出被消费。解锁脚本是每一笔比特币交易输入的一部分，而且往往含有一个由用户的比特币钱包（通过用户的私钥）生成的数字签名。由于解锁脚本常常包含一个数字签名，因此它曾被称作ScriptSig。

每一个比特币验证节点会通过同时执行锁定和解锁脚本来验证一笔交易。每个输入都包含一个解锁脚本，并引用了之前存在的UTXO。 验证软件将复制解锁脚本，检索输入所引用的UTXO，并从该UTXO复制锁定脚本。 然后依次执行解锁和锁定脚本。 如果解锁脚本满足锁定脚本条件，则输入有效（请参阅单独执行解锁和锁定脚本部分）。 所有输入都是独立验证的，作为交易总体验证的一部分。

### 数字签名

比特币中使用的数字签名算法是椭圆曲线数字签名算法（Elliptic Curve Digital Signature Algorithm）或ECDSA  
数字签名在比特币中有三种用途（请参阅下面的侧栏）。第一，签名证明私钥的所有者，即资金所有者，已经授权支出这些资金。第二，授权证明是不可否认的（不可否认性）。第三，签名证明交易（或交易的具体部分）在签字之后没有也不能被任何人修改。  
每个交易输入都是独立签名的  

## 比特币网络

比特币采用了基于国际互联网（Internet）的P2P（peer-to-peer）网络架构。P2P是指位于同一网络中的每台计算机都彼此对等，各个节点共同提供网络服务，不存在任何“特殊”节点。每个网络节点以“扁平（flat）”的拓扑结构相互连通。 在P2P网络中不存在任何服务端（server）、中央化的服务、以及层级结构。P2P网络的节点之间交互运作、协同处理：每个节点在对外提供服务的同时也使用网络中其他节点所提供的服务。P2P网络也因此具有可靠性、去中心化，以 及开放性。  

尽管比特币P2P网络中的各个节点相互对等，但是根据所提供的功能不同，各节点可能具有不同的角色。每个比特币节点都是路由、区块链数据库、挖矿、钱包服务的功能集合。

## 挖矿和共识

“挖矿”这个词有点误导。 一般意义的挖矿类似贵金属的提取，更多将人们的注意力集中到创造每个区块中获得的奖励。 虽然挖矿能够获得这种奖励作为激励，但挖矿的主要目的不是这个奖励或者产生新币, 挖矿是一种机制，这种机制实现了去中心化的安全。

