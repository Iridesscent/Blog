---
title: apt-get 主要命令
tags:
  - Terminal
id: 49
categories:
  - Linux
date: 2016-02-09 12:45:14
---

<pre class="lang:default decode:true  " title="apt-get命令">apt-cache search package 搜索软件包

apt-cache show package  获取包的相关信息，如说明、大小、版本等

sudo apt-get install package 安装包

sudo apt-get install package --reinstall   重新安装包

sudo apt-get -f install   修复安装

sudo apt-get remove package 删除包

sudo apt-get remove package --purge 删除包，包括配置文件等

sudo apt-get update  更新源

sudo apt-get upgrade 更新已安装的包

sudo apt-get dist-upgrade 升级系统

apt-cache depends package 了解使用该包依赖那些包

apt-cache rdepends package 查看该包被哪些包依赖

sudo apt-get build-dep package 安装相关的编译环境

apt-get source package  下载该包的源代码

sudo apt-get clean &amp;&amp; sudo apt-get autoclean 清理无用的包

sudo apt-get check 检查是否有损坏的依赖</pre>
&nbsp;