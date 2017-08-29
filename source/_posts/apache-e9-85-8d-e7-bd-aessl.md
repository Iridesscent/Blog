---
title: apache配置ssl
id: 78
categories:
  - Linux
date: 2016-02-20 00:55:15
tags:
  - ssl
---

没有黑的意思，不过百度到的配置方式确实太辣鸡了，似懂非懂的帖来帖去。

贴两个英文文档，写的非常专业全面。

*   [How To Create a SSL Certificate on Apache for Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-apache-for-ubuntu-14-04)
*   [Switch to HTTPS Now, For Free](https://konklone.com/post/switch-to-https-now-for-free)
按照第一个配置完了以后就可以用https访问了，但是用的是使自己生成的证书，浏览器会提示证书不安全。

这时候吧crt文件和key文件换乘第二个文档里生成的就行了

但是这时之前安装好的wordpress会有外链问题

进一步可以配置一下wordpress，使之支持https

[How to Add SSL and HTTPS in WordPress](http://www.wpbeginner.com/wp-tutorials/how-to-add-ssl-and-https-in-wordpress/)

期间如果浏览器显示的界面没反应可以清除一下cookie