---
layout:     post
title:      "使用 WordPress 快速搭建一个网站"
subtitle:   "使用 WordPress 快速搭建一个网站"
date:       2017-05-15 12:00:00
author:     "你看那朵乌云儿"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 其他
---

## WordPress 简介
WordPress 是一个以 PHP 和 MySQL 为平台的自由开源的博客软件和内容管理系统。WordPress 具有插件架构和模板系统。 Alexa 排行前100万的网站中有超过16.7%的网站使用 WordPress。 WordPress 有丰富的模板类型, 我们可以使用它来快速搭建一个个人博客或者公司官网.

## 准备工作

购买服务器和域名

## 流程

购买服务器 => 购买域名 => 域名解析 => 服务器环境搭建 => WordPress 安装 => WordPress 模板修改

**域名解析**: 计算机只能识别 IP 地址, 而我们常用的域名计算机是不能直接识别的, 所以需要域名解析服务, 也就是把域名解析成其指向的 IP 地址. 

## 服务器环境

配置 LAMP Stack, 即 Linux + Apache + MySQL+ PHP. 服务器选用新版本 Ubuntu. 安装教程网上有很多, 失败了多 Google, 实在不行推倒重来.

几个 tips: 

- PHP 版本建议7+, 但不要最新. 觉个栗子, 我踩坑发现 7.1 使用不错, 7.2 版本部分插件有兼容性问题.  
- 和 MySQL, Apache 配套, PHP 需要安装一些插件, 别遗漏了. 否则在 Apache 服务器上 PHP 不会被执行. 可以试试这样安装, 别忘了改版本号. `sudo apt-get install php7.0-mysql php7.0-curl php7.0-json php7.0-cgi  php7.0 libapache2-mod-php7.0`
- WordPress 部分插件需要额外的 PHP 模块安装, 比如 WPML 需要 XML 解析.
- 需要在 MySQL 配置 WordPress 需要的用户
- WordPress 一般需要修改 PHP 的配置文件, 增大上传文件大小等. 对应配置文件在 `/etc/php/7.1/apache2/php.ini`

## WordPress 安装

部分教程是下载 WordPress 到本地, 然后 FTP 上传到服务器, 其实也可以直接在服务器上通过 curl 命令下载安装, 这样更方便. 

	`curl -O https://wordpress.org/latest.zip`

将 解压后的 WordPress/ 下的文件全部移动到 /var/www/html, 该目录是你网站的根目录. 修改 wp-config.php 文件, 填入 MySQL 等配置信息.

## wordpr 主题与插件

WordPress 本身只是一套程序, 我们需要的网页表现由相应的主题来提供. 一些优秀的主题需要自己购买.  

可以在后台 index.php/wp-admin 安装主题和插件. 如果是从其他网站下载的主题或者插件, 可以通过 FTP 上传到 wp-content 目录下的相应位置, 然后通过后台界面安装.

Mac FTP 用法:  

	Terminal => shell, 新建远程连接 => sftp, 输入服务器用户名和 IP
	=> 在打开的终端输入密码 => 执行命令 put local-file [remote-file]


## HTTPS

Let’s Encrypt 可以提供免费的 HTTPS 授权, 使用也非常简单. 使用 [Certbot](https://certbot.eff.org/) 几分钟就可以轻松搞定 HTTPS. 需要注意的是, 在服务器部署好了 HTTPS 之后, 需要在 WordPress 后台-Setting-General 设置一下 WordPress Address 和 Site Address.  

### 踩坑

**从 HTTPS 恢复 HTTP**

如果你希望仍然使用 HTTP 访问你的网站, 也就是说你在服务器移除掉了 HTTPS 证书. 因为 WordPress 之前的 Site Address 还是设置的 HTTPS, 所以就算你在浏览器输入 HTTP 也会自动301重定向到 HTTPS. 因为此时服务器已经没有证书, 所以网站打不开, 也无法进入后台.

**解决方案**

WordPress 的配置信息都在数据库, 我们可以通过服务器修改数据库, 从而解决这个 bug. 不同的主题具体数据库结构可能会有所不同, 但应该都能够通过清晰的命名来识别对应的表和字段. 举个栗子:  

登录服务器 => 登录 MySQL => 找到 wp_options
	
	mysql -u root -p
	use wordpress;
	show tables;
	select * from wp_options limit 10;
	update wp_options set option_value = "http://www.tokenall.io" where option_name = "siteurl";

