
---
layout:     post
title:      "科学上网"
date:       2019-04-28 15:31:00
author:     "你看那朵乌云儿"
header-img: "img/black-hole.jpg"
catalog: true
tags:
    - 其他
---

首先问候 GFW，FUCK！！！  
真他妈有病。你们在害怕什么！  
最他妈的烦的是，连 GitHub 都经常用不了。  
我承认大多数国人的确因为长期落后的教育系统，很容易被外网的乱七八糟的信息带偏。但你他妈的一天都搞愚民教育，有几个人能够有正常的价值观世界观！  
**搜索引擎是一个普通人通向互联网的第一入口，而百度往往把这些普通人推向深渊。**


# SS 账号

搬瓦工支持一键配置 shadowsocks。其他服务器需要手动搭建。

# 客户端

**Mac：ShadowsocksX-NG-R**

Mac：ShadowSocksR（ssr）为ShadowSocks（ss）加强版。  

下载：https://github.com/qinyuhang/ShadowsocksX-NG-R/releases

**iOS：Wingy**  
非大陆 App Store 下载，免费，只支持 shadowsocks。

**iOS：Shadowrocket**  
非大陆 App Store 下载，收费，支持 shadowsocks 和 shadowsocksR。  
最好用最强大的 iOS 客户端。

# 终端翻墙

解决 GitHub 半天 clone 不下来的问题，操。  
MAC 终端，默认不走代理服务器；即浏览器已经可以翻墙，但是终端不行。
直接设置终端的代理, 需要先配置好 shadowsocksX，确保浏览器能够正常翻墙。

     export http_proxy=socks5://127.0.0.1:1080 # 配置http 代理访问
     export https_proxy=socks5://127.0.0.1:1080 # 配置https 代理访问
     export all_proxy=socks5://127.0.0.1:1080 # 配置http和https访问
     unset http_proxy  # 取消http 代理访问
     unset https_proxy # 取消https 代理访问
     
端口请与 ss 客户端设置中的 socks5 代理端口保持一致。  
执行后，只对当前终端起作用；重启终端后，默认失效。    



